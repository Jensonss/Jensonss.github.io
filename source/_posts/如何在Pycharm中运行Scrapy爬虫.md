---
title: 如何在Pycharm中运行Scrapy爬虫
date: 2017-08-17 20:24:50
tags: Python
categories: Python
---

# 0x00 前言

学爬虫就绕不过Scrapy，但是发现每次运行爬虫都要使用shell命令，

但是在终端窗口看log着实让人难受，想着如何能让Scrapy在Pycharm执行。

# 0x01 解决

办法总比问题多，没有找到直接运行项目的方法点。但是曲线救国也未尝不可。

既然关于Scrapy的教程运行爬虫时都是shell命令，那就让python文件中执行这个shell命令。

命令是`comm='scrapy crawl name'`

那如何让python运行shell命令呢？参考[Python中执行shell命令](http://www.jensondev.me/2017/08/17/Python%E4%B8%AD%E6%89%A7%E8%A1%8Cshell%E5%91%BD%E4%BB%A4/)

在项目下创建main.py文件：

```python
import os

comm = 'scrapy crawl jianshu'
res = os.popen(comm).read()
print(res)
```

这样每次执行这个python文件就可以启动爬虫项目了，然后在pycharm控制台查看打印消息即可。