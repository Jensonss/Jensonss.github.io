---
title: Mac版eclipse中每次重启电脑后字体自动变小
date: 2017-04-22 18:50:50
tags: 工具
categories: 工具
---
如题每次开机后打开eclipse时，发现上次调好的字体又变小了。每次都这样，比较影响心情。今天决定把这个问题解决掉。
网友的做法是：
打开 应用程序/Eclipse.app/Contents/Eclipse/eclipse.ini 文件中，
把文中“-Dorg.eclipse.swt.internal.carbon.smallFonts”删掉。
这样可以避免Eclipse里面的字体奇怪地自动变小。。

发现很久以前就存在这个问题，不过当时的fix方法和最新的不一样。
[传送通道](http://t-machine.org/index.php/2011/08/21/fix-eclipses-fonts-on-os-x/)

但是上面的2方法好像对我来说没有效果(我只试了第一种)。后来在偏好设置里发现Theme为mac时使用的是Reduced Palette这个选项，我好奇的把选项改为了Default。然后点了确认再调整好需要的字体并重启。然后真的没有再恢复小字体了。

![屏幕快照 2017-03-08 下午12.04.34.png](http://upload-images.jianshu.io/upload_images/1796052-3ea1ef0bbc0ed211.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)