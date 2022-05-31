# SSH 工具介绍及常见使用方法
## SSH介绍
SSH 为 Secure Shell 的缩写。  SSH是一种网络协议，用于计算机之间的加密登录。如果一个用户从本地计算机，使用SSH协议登录另一台远程计算机，我们就可以认为，这种登录是安全的，即使被中途截获，密码也不会泄露。
SSH主要用于远程登录。假定你要以用户名user，登录远程主机host，只要一条简单命令就可以了。
## 常见使用方法
### 进行远程登录
1. `ssh user@host`：最简单的远程登录方法，默认对目标主机22端口建立连接
2. `ssh user@host -p portnumber`：指定目标主机的连接端口
	 第一次登录会产生如下系统提示：
	```
	$ ssh user@host
	The authenticity of host ‘host (12.18.429.21)’ can’t be established.
	RSA key fingerprint is 98:2e:d7:e0:de:9f:ac:67:28:c2:42:2d:37:16:58:4d.
	Are you sure you want to continue connecting (yes/no)?
	```
	输入`yes`并且输入登录密码之后，本地便将目标主机的公钥保存在`user/.ssh/known_host`文件之中，下次登录就可以省去此步警告，直接输入密码登录
1. 公钥登录方法  
	参考: [[工具/SSH 配置公钥登录]]
	
	使用密码登录，每次都必须输入密码，非常麻烦。好在SSH还提供了公钥登录，可以省去输入密码的步骤。
	所谓”公钥登录”，原理很简单，就是用户将自己的公钥储存在远程主机上。登录的时候，远程主机会向用户发送一段随机字符串，用户用自己的私钥加密后，再发回来。远程主机用事先储存的公钥进行解密，如果成功，就证明用户是可信的，直接允许登录shell，不再要求密码。
	这种方法要求用户必须提供自己的公钥。如果没有现成的，可以直接用ssh-keygen生成一个：
	
		ssh-keygen

	运行上面的命令以后，系统会出现一系列提示，可以一路回车。其中有一个问题是，要不要对私钥设置口令（passphrase），如果担心私钥的安全，这里可以设置一个。

	运行结束以后，在`$HOME/.ssh/`目录下，会新生成两个文件：`id_rsa.pub`和`id_rsa`。前者是公钥，后者是私钥。

	这时再输入下面的命令，将公钥传送到远程主机host上面：

		$ ssh-copy-id user@host

	上述命令只是传输公钥的一种方法，当然也可以使用其他的方法进行文件传输，如SCP以及命令行工具（Xshell，MOBAXtreme等）自带的工具进行秘钥的传输。从此再登录，就不需要输入密码了。
	如果还是不行，就打开远程主机的/etc/ssh/sshd_config这个文件，检查下面几行前面”#”注释是否取掉。

		RSAAuthentication yes  
		PubkeyAuthentication yes  
		AuthorizedKeysFile .ssh/authorized_keys

	然后，重启远程主机的ssh服务。

		// ubuntu系统  
		service ssh restart
		// debian系统  
		/etc/init.d/ssh restart
		
### 远程操作与端口转发
1. 简单的命令的执行：
	 通常对远程主机的操作可以直接通过SSH远程登录进行操作，有时命令较少时可以直接使用SSH命令在远程主机上执行命令，如`ssh -user@host -p port 'ls' `表示在远程主机上执行`ls`命令。
2. 本地端口转发
	假定host1是本地主机，host2是远程主机。由于种种原因，这两台主机之间无法连通（防火墙等等）。但是，另外还有一台host3，可以同时连通前面两台主机。因此，很自然的想法就是，通过host3，将host1连上host2。在host1上执行下面命令即可：
		
		ssh -L 1111:host2:2222 host3
	命令中的L参数一共接受三个值，分别是”本地端口:目标主机:目标主机端口”，它们之间用冒号分隔。这条命令的意思，就是指定SSH绑定本地端口1111，然后指定host3将所有的数据，转发到目标主机host2的2222端口。
	这样一来，我们只要连接host1的2121端口，就等于连上了host2的21端口。
	“本地端口转发”使得host1和host3之间仿佛形成一个数据传输的秘密隧道，因此又被称为”SSH隧道”。
	下面是一个比较有趣的例子。

		ssh -L 5900:localhost:5900 host3

	它表示将本机的5900端口绑定host3的5900端口（这里的localhost指的是host3，因为目标主机是相对host3而言的，也就是在host3上的localhost）。
	另一个例子是通过host3的端口转发，ssh登录host2。

		ssh -L 9001:host2:22 host3

	这时，只要ssh登录本机的9001端口，就相当于登录host2了。

		 ssh -p 9001 localhost

	上面的-p参数表示指定登录端口。
	通过跳板机host2的port2端口在本地host1的port1端口运行远端服务器host3的port3端口上的jupyter notebook服务：
	
		ssh -N -L port1:localhost:port3 user@host2 -p port2
	
	从上面的命令中可以看出，`-L`之后的命令可以理解是在host3上执行的。
3. 远程端口转发
	既然”本地端口转发”是指绑定本地端口的转发，那么”远程端口转发”（remote forwarding）当然是指绑定远程端口的转发。

	还是接着看上面那个例子，host1与host2之间无法连通，必须借助host3转发。但是，特殊情况出现了，host3是一台内网机器，它可以连接外网的host1，但是反过来就不行，外网的host1连不上内网的host3。这时，”本地端口转发”就不能用了，怎么办？

	解决办法是，既然host3可以连host1，那么就从host3上建立与host1的SSH连接，然后在host1上使用这条连接就可以了。

	我们在host3执行下面的命令：

		ssh -R 2121:host2:21 host1

	R参数也是接受三个值，分别是”远程主机端口:目标主机:目标主机端口”。这条命令的意思，就是让host1监听它自己的2121端口，然后将所有数据经由host3，转发到host2的21端口。由于对于host3来说，host1是远程主机，所以这种情况就被称为”远程端口绑定”。
	绑定之后，我们在host1就可以连接host2了：

		$ ftp localhost:2121

	这里必须指出，”远程端口转发”的前提条件是，host1和host3两台主机都有sshD和ssh客户端。
	
参考资料：
[## SSH原理与使用教程](https://www.tianqiweiqi.com/ssh-study.html)

