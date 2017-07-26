---
title: Python自动化之hexo自动发布和提交源码
date: 2017-07-26 16:43:38
tags: [Python,自动化]
categories: [Python,自动化]
---

# 0x00 前言

现在的博客系统是使用hexo+github搭建的静态页面系统。

博客文章页面和源码都由github托管。所以每次发布文章时要跳转到相应目录下，执行`hexo d -g`命令发布新文章。如果要同步源码方便其他机器也能写文章，那么还要使用`git add .`添加暂存，`git commit -m message`添加本地和`git push`推送远程分支。

# 0x01 自动化设想

开始因为新鲜感这些操作都还好，顺便练手。但是时间久了就觉得每次都操作这么多次太浪费时间了。

这时候就萌生了自动化命令的想法，一步操作完成所有这些命令，该多省心！

# 0x02 自动化设计

实现自动化首先想到的是Python，而且最近也学了不久，练练手也刚好。

由于都是终端命令，所以要先实现让Python执行终端命令的方法：

①通过`os.system`方法

②通过`subprocess.Popen`方法

通过使用发现都可以执行命令，但是如果命令分开执行，命令之间的前后结果不会关联起来，比如我第一个命令执行`cd /jenson/`，再执行第二个命令`pwd`时时发现路径仍然是旧路径。

所以现在面临新的问题：多个命令一起执行。如何一起执行呢？

其实上面①和②都支持多命令一起，譬如：

如果多个命令共同执行，不管前一个命令是否成功，都要执行后一个则使用**&&**连接命令：

- `os.system('cmd1 && cmd2')`
- `subprocess.Popen('cmd1 && cmd2', shell=True)`

如果前一个命令执行成功才执行后一个命令，则使用**;**连接：

- `os.system('cmd1 ; cmd2')`
- `subprocess.Popen('cmd1 ; cmd2', shell=True)`

接下来代码实现：

# 0x03 自动化实现

创建`AutoCommit.py`文件

```python
import subprocess

# 跳转工作目录
to_path = 'cd ~/Downloads/hexo/'
# 发布命令
to_deploy = 'hexo d -g'
# git add命令
to_add = 'git add .'
# git commit命令
to_commit = 'git commit -m 新添加文章' 
# git push命令
to_push = 'git push origin hexoSource'
tmp = subprocess.Popen(to_path + '&&' +
                       to_deploy + '&&' +
                       to_add + '&&' +
                       to_commit + '&&' +
                       to_push, shell=True)
```

这就算完成了，运行`python3 AutoCommit.py `时候也能正常提交。

但是这样感觉还能再完善些，继续改造：

# 0x04 自动化优化

首先关于git commit的信息，我想实现自定义，而不是每次都固定这几个内容，这样不容易区分每次版本提交了哪些内容。

现在的初步想法是在终端执行`python3 AutoCommit.py`命令时，同时接收命令行参数作为提交的内容，比如这样`python3 AutoCommit.py  message`。

Python文件要接收命令行参数，需要使用`sys`模块，接收参数：

`message = sys.argv[1]`。

这里argv[0]表示脚本名，argv[1]开始才表示后面的参数

同时接收message后判断是否为空，如果为空在使用默认提交信息，所以封装一个方法

```python
def isNullMessage(message):
    """
    判断消息是否为空,如果为空赋默认值
    :param message:
    :return:
    """
    if len(message.strip()) == 0:
        message = '提交新文章'
    return message
```

既然判断提交信息都封装到方法了，那么这一些列命令也应该封装到方法中：

```python
def deployAndGitPush(message):
    """
    hexo静态页面发布和源码push git服务器
    :return:
    """
    # 跳转工作目录
    to_path = 'cd ~/Downloads/hexo/'
    # 发布命令
    to_deploy = 'hexo d -g'
    # git add命令
    to_add = 'git add .'
    # git commit命令
    to_commit = 'git commit -m ' + message
    # git push命令
    to_push = 'git push origin hexoSource'
    tmp = subprocess.Popen(to_path + '&&' +
                           to_deploy + '&&' +
                           to_add + '&&' +
                           to_commit + '&&' +
                           to_push, shell=True)
```

最终调用如下：

```python
deployAndGitPush(message=isNullMessage(message=message))
```



OK，自动化优化完成。执行`python3 AutpCommit.py Android文章提交`。

