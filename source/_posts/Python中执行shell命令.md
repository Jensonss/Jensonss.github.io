---
title: Python中执行shell命令
date: 2017-08-17 20:07:49
tags: Python
categories: Python
---

# 0x00前言

这几天频繁用到python和shell命令打交道。所以就总结下在python中执行shell命令的几种方式



# 0x01 方式

## os模块

- os.system('ls')

  这个调用相当直接，且是同步进行的，程序需要阻塞并等待返回。返回值是依赖于系统的，直接返回系统的调用返回值，所以windows和linux是不一样的。

  **该方法没有返回值**


- os.popen('ls')

  popen方法通过p.read()获取终端输出，而且popen需要关闭close().当执行成功时，close()不返回任何值，失败时，close()返回系统返回值. 可见它获取返回值的方式和os.system不同。

## commands模块

​	根据你需要的不同，commands模块有三个方法可供选择。getstatusoutput, getoutput, getstatus。

```
>>> import commands
>>> commands.getstatusoutput('ls /bin/ls')
(0, '/bin/ls')
>>> commands.getstatusoutput('cat /bin/junk')
(256, 'cat: /bin/junk: No such file or directory')
>>> commands.getstatusoutput('/bin/junk')
(256, 'sh: /bin/junk: not found')
>>> commands.getoutput('ls /bin/ls')
'/bin/ls'
>>> commands.getstatus('/bin/ls')
'-rwxr-xr-x  1 root        13352 Oct 14  1994 /bin/ls'

```

**该模块在python3.6中已经不存在，不知道具体从哪个版本移除了，所以这部分代码来自网友分析**

## subprocess模块

```python
subprocess.Popen('ls', shell=True)
```

shell=True表示命令最终在shell中运行。Python文档中出于安全考虑，不建议使用shell=True。建议使用Python库来代替shell命令，或使用pipe的一些功能做一些转义。

如果是多条命令可以使用‘&&’

```python
subprocess.Popen('ls&&pwd', shell=True)
```

