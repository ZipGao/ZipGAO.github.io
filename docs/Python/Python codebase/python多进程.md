---
layout: default
title: Python 多进程
date: 2020-05-31
categorires: Python
grand_parent: Python
parent: Python codebase
Created by: Anonymous
Tags: python, 进程
---
[toc]
# Python多进程


<small>Created: 2020-05-31</small>  
<small>Tags: #python, #多进程</small>

多进程可以用于提高代码的运行速度，尤其是在批量处理数据的时候，Python 多进程主要使用`multiprocess` 库，下边通过一个例子介绍其用法

### 问题背景：对一个长列表进行遍历，对于其中的元素进行操作

定义对元素进行操作的函数为`func_item`，遍历列表并对其进行逐元素操作的方法为`func_list`，不使用多进程时，代码如下所示：

```python
def func_item(item):
    pass
def func_list(l:list):
    for item in l.items():
        func_item(item)
def main():
    l = list()
    func_list(l)
```

上述代码为从前往后对列表进行遍历，效率较低。

改成多进程后，代码如下：

```python
import multiprocess
# 遍历以及操作的函数不变
def func_item(item):
    pass
def func_list(l:list):
    for item in l.items():
            func_item(item)
# 在main函数中做如下改动：
def main():
    pool = multiprocess(10) # 申请10个进程池
    l = list()
    for i in range(100):
        if i*10000>len(l):
            break
        # 对于每个进程传入一个子列表
        pool.apply(func= func_list, args = (l[i:min(i*10000,len(l))]))
    # 这里的pool.close()是说关闭pool，使其不在接受新的（主进程）任务
    pool.close()
    # 这里的pool.join()是说：主进程阻塞后，让子进程继续运行完成
    # 子进程运行完后，再把主进程全部关掉。
    pool.join()
```