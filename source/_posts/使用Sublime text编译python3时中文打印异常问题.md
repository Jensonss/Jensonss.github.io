---
title: 使用Sublime text编译python3时中文打印异常问题
date: 2017-04-22 18:55:54
tags: Python
categories: Python
---

按照网上的一些方法改了几个地方都是没有效果，就又都给还原回来了。后来发现只要修改先前配置的环境即可
```
{
	"cmd": ["/Library/Frameworks/Python.framework/Versions/3.5/bin/python3","-u","$file"],
	"env": {"LANG": "en_US.UTF-8"}
}
```
加上一句env的配置就可以了。
什么？不知道去哪里找配置文件了？

![屏幕快照 2017-01-07 上午11.35.31.png](http://upload-images.jianshu.io/upload_images/1796052-06e6c7cff8200bb5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

打开Preferences -> Browse Packages -> 会自动打开一个新的窗口-打开user编辑之前的文件保存即可。
![屏幕快照 2017-01-07 上午11.36.46.png](http://upload-images.jianshu.io/upload_images/1796052-a48410d9dd488b78.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![屏幕快照 2017-01-07 上午11.37.09.png](http://upload-images.jianshu.io/upload_images/1796052-50806a0e72ca3dc2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

mark一下