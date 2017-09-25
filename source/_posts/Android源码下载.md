---
title: Android源码下载
date: 2017-04-21 20:55:54
tags: Android
categories: Android
---

#### 说在前面的话
win系统的笔记本过年回来就没有带回北京。最近打算看看Android源码，所以需要在mac中从新下载。
#### 从官网下载
下载Android源码首先想到的就是[官网](https://source.android.com/source/downloading.html)
按照指示要先安装repo。但是在执行
```
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
```

时候就出现异常

```
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:--  0:01:14 --:--:--    0
curl: (7) Failed to connect to storage.googleapis.com port 443: Operation timed out
```

<!-- more -->

搜索了下异常发现很早就有这个问题，比如[这里](https://github.com/npm/npm/issues/10037)
但是好像没什么好的解决方法。网传有其他地址可以使用，但是没有继续浪费时间了，不能一条路走到黑。

#### 使用国内资源
国外太麻烦，毕竟只是想看源码，不想在下载方面浪费太多精力。所以把想法转战到国内的源上。
分享2个源，[科大源](https://lug.ustc.edu.cn/wiki/mirrors/help/aosp#已有仓库如何改用科大源)和[清大源](https://mirrors.tuna.tsinghua.edu.cn/help/AOSP/)
其中我选择使用的是科大源，打开页面看到初始同步有两个方法，方法1毕竟简单，下载一个打好的包，自己repo sync。方法2还是要下载repo，而且如果中途断掉还要从头下载，所以果断选了方法1，直接使用迅雷下载tar包还是很快的。
不到一个小时下载完成，解压后打开aosp文件夹发现没有文件，因为是.开头的，都是隐藏文件，显示后如图：
![屏幕快照 2017-03-16 下午1.55.14.png](http://upload-images.jianshu.io/upload_images/1796052-a48a38e120b53440.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
执行
```
/Users/jenson/Downloads/aosp/.repo/repo/repo sync
```

同步

![屏幕快照 2017-03-16 下午2.08.54.png](http://upload-images.jianshu.io/upload_images/1796052-185f5625074cf187.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 如何修改已经init的repo
如果想下载其他版本的源码，但是repo已经初始化过，
如果再次执行repo init xxx会提示 :
```
Your identity is: jenson <xxx@foxmail.com>
If you want to change this, please re-run 'repo init' with --config-name
repo has been initialized in /Users/jenson/Downloads/aosp
```

上面意思是如果要修改init，需要重新运行repo init +新的url +config
这时会自动修改配置并初始化:

```
/Users/jenson/Downloads/aosp/.repo/repo/repo init -u https://aosp.tuna.tsinghua.edu.cn/platform/manifest -b android-6.0.1_r58  --config
```