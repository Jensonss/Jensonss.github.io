---
title: Python在Windows下提示ModuleNotFoundError No module named win32api
date: 2017-08-19 11:08:04
tags: Python
categories: Python
---

# 0x00 前言

在Windows下运行scrapy时提示了这个异常:`ModuleNotFoundError No module named win32api`。

# 0x01 解决

这是因为windows下的Python开发环境缺少Python和系统API之间调用的这一个库。

可以从[这里下载](https://sourceforge.net/projects/pywin32/files/pywin32/)对应版本的库，如果不知道下载哪个可以使用**pip install pypiwin32**命令直接安装。

命令行安装结果如下：

```
  Downloading pypiwin32-220-cp36-none-win_amd64.whl (9.0MB)
    100% |████████████████████████████████| 9.0MB 78kB/s
Installing collected packages: pypiwin32
Successfully installed pypiwin32-220
```

