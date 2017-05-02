---
title: Python入门系列(0):OSX系统下Python3的配置与安装
date: 2017-04-22 18:55:54
tags: Python
categories: Python
---
# 关于Python默认安装的问题
在Linux和osx中都默认集成了Python，这时我们要查看集成的版本是不是我们需要的3.x。如果是，那么恭喜你不用往下看了，如果不是请继续。
如何查看Python版本呢？打开终端输入如下命令即可：
```
python --version
```

![屏幕快照 2017-01-06 下午6.45.53.png](http://upload-images.jianshu.io/upload_images/1796052-8911115b3e35e144.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到我的系统默认的版本是2.7.10，看来还是需要安装3.x的。咱门继续

# Python3下载与安装

先附上[python3.6](https://www.python.org/ftp/python/3.6.0/python-3.6.0-macosx10.6.pkg)下载地址。
下载完成直接点击安装即可，一直点击继续或者同意操作，直到出现如下界面：

![屏幕快照 2017-01-06 下午6.10.33.png](http://upload-images.jianshu.io/upload_images/1796052-79c9c599a48d6f23.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

关闭安装窗口，如果此时已经打开了terminal终端需要重新启动，来使刚才的安装生效(*osx下Python安装后不需要配置类似jdk的环境变量*)
需要注意一点的是 2.x版本使用Python命令，在3.x下需要在后加上3

```
python3 --version
```

查看输出结果，发现已经能识别出3.x版本，至此Python3安装完毕。

![屏幕快照 2017-01-06 下午7.01.29.png](http://upload-images.jianshu.io/upload_images/1796052-ec941e46202d0d97.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)