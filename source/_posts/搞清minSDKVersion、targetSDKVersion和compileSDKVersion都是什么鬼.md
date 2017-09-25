---
title: 搞清minSDKVersion、targetSDKVersion和compileSDKVersion都是什么鬼
date: 2017-04-21 20:55:54
tags: Android
categories: Android
---
>一直以来对这几个SDK版本概念都有点模糊不清，对于API的使用又会产生什么样的影响。所以今天花点时间来记录下。



![屏幕快照 2017-04-19 下午12.09.45.png](http://upload-images.jianshu.io/upload_images/1796052-947f3215a5f6169e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#### minSDKVersion
顾名思义是设置sdk最低版本的。作用就是操作系统会拒绝低于
该标准的APP的安装。

例如，minSDKVersion设置为16(Jelly Bean 4.1系统)，那么该APP将只能运行在4.1系统以上的设备中，想要在2.3系统上安装是不被允许的。

minSDKVersion比较容易理解，经常让我混淆的时其他两个版本设置会对API产生的影响。

<!-- more -->

#### targetSDKVersion
targetSDKVersion就是设置SDK目标版本，目标版本的设置就是为了告诉Android系统：本APP是设计计划给哪个API级别运行的。

一般情况下目标版本设置为当前Android最新版本即可。既然是一般那也就有特殊情况，什么情况下需要修改目标版本呢？

如果新发布的SDK版本会对UI显示甚至操作系统运行机制产生影响，而你的APP又没有做好应对措施，为了保证你的APP正常运行，那你需要降低目标版本。因为你的目标版本仍然是旧的SDK，所以在新版系统中那些新的变化会在你的APP中被忽略，继而保证其正常运行。

例如，Android6.0系统增加了动态权限机制，如果为了追时髦，盲目把你的targetSDKVersion设置为23(6.0)，那么在需要使用权限的地方将会出现异常。为此，在你做好动态权限申请之前，为保障APP正常运行，你需要将目标版本设置低于23。

#### compileSDKVersion
compileSDKVersion是设置编译版本。

一般来说编译目标版本是选择最新的SDK，这样可以及时使用体验到新的API提供的新功能。

值得注意的是，如果minSDKVersion和compileSDKVersion版本差距比较大的话，可能会造成API的不兼容。例如，你的最低版本是2.3 ，但是编译版本是5.0，API中使用了4.0SDK提供的一些新API，这样的后果是在2.3系统中运行到该处代码时会发生异常崩溃。这是因为代码的不兼容造成的。如图：

![屏幕快照 2017-04-19 下午1.05.00.png](http://upload-images.jianshu.io/upload_images/1796052-67bfb13669a8dce2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如何解决这种API的不兼容呢？
一种办法是提升minSDKVersion到新API使用的SDK版本，但是这种方法只是回避兼容性，并没有确实解决问题，而且还要放弃低版本部分市场。
比较好的做法是在使用新API地方做设备版本的检查。Build.VERSION_SDK_INT常量表示当前Android设备的版本号。可以将该常量同新API版本进行比较，如果版本大于等于新版API版本号，则正常使用新API功能，否则使用旧的调用。兼容设置如下：

![屏幕快照 2017-04-19 下午1.16.40.png](http://upload-images.jianshu.io/upload_images/1796052-a2d7e548c4a118cf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>注意：compileSDKVersion是和编译器打交道的，而minSDKVersion和targetSDKVersion是和系统打交道的。
