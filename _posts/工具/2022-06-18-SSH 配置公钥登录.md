## SSH 配置公钥登录

配置公钥可以实现本机免密登录

请在常用个人机器上如下配置

mac用户：

1.  在本地机器上，打开终端terminal；
    
2.  输入ssh-copy-id -p 1500 $USER@222.195.93.60，按提示输入服务器密码
    
3.  直接ssh -p 1500 $USER@222.195.93.60 （部分ssh版本是 ssh $USER@222.195.93.60 1500）免密登录
    

windows用户：

1.  在本地机器上，打开cmd，输入ssh-keygen，生成~/.ssh/id_rsa与~/.ssh/id_rsa.pub两个文件
    
2.  将生成的id_rsa.pub上传至服务器：scp -p 1500 ~/.ssh/id_rsa.pub $USER@222.195.93.60:/data/$USER
    
3.  在服务器上，cat ~/id_rsa.pub >> ~/.ssh/authorized_keys 即可测试免密登录
    

公钥传输也可以用带有scp功能的终端（例如mobaxterm左侧scp栏）直接传输上去