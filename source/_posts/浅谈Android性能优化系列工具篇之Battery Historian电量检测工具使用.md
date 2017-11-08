---
title: 浅谈Android性能优化系列工具篇之Battery Historian电量检测工具使用
date: 2017-11-09 00:30:15
tags: Android
categories: Android
---

# 前言

Battery Historian是谷歌开放的电量检测工具，目前发布2个版本：首个版本是由python编写，但是在2015年谷歌IO大会发布的Battery Historian2.0版本使用Go重写，新版本提供的信息更全面。

去[Google的github项目](https://github.com/google/battery-historian)下载Battery Historian。

# 准备

## 电池重置

使用命令`adb shell dumpsys batterystats --reset`重置电池

**电池重置的目的是为了让下次电量统计时从当前时间开始，而不是从最初开机或上次充满电(具体何时开始不清楚)。**

## 开启全部wakelock

使用命令`adb shell dumpsys batterystats --enable full-wake-history`开启全部wakelock

# 导出统计数据

接下来应该断开设备的充电连接，对我们的app进行各种操作，等操作完成重新连接设备，然后进行电量统计。

其实电量统计系统已经为我们做了，我们要做的就是把电量统计信息以文件形式导出到我们的电脑。

老版本的导出命令为：`adb shell dumpsys batterystats > out.txt `。

`adb shell dumpsys batterystats > 包名 > out.txt `得到给定的app相关的电量消耗信息

新版本的导出命令为：` adb bugreport out.zip`，也可以使用`adb bugreport > out.txt`。

新版本同样也可以统计指定APP

导出的out.txt文件在你命令行执行时的所属目录。

### 异常1

这里我出现一个异常：当前SDK最新版本为7.0(25)，而我的手机系统版本为5.0，

所以使用命令行：**adb bugreport > report.txt**时出现了如下异常：

**Failed to get bugreportz version, which is only available on devices running Android 7.0 or later.**

这是由于SDK版本太新了，既然手机系统不能升级，那就只好把SDK降级，

其实这里只是使用了plantform下的adb命令，所以从新下载**platform-tools**即可。

我下载和手机系统版本一致的platform-tools，然后解压。

使用新下载的platform-tools的adb命令执行`adb bugreport > out.txt`时提示没有设备。

### 异常2

又使用**adb devices**查看列表仍然没有。重启adb也没用效果。

因为我的是Mac设备，所以命令行执行`system_profiler SPUSBDataType`时会显示一系列外接设备信息，找到你的Android设备，复制Vendor ID。

进入你下载的低版本**platform-tools**文件夹下，修改或创建**adb_usb.ini**文件，把刚才复制的Vendor ID粘贴到这里。

再次执行**adb devices**就可以查看到设备列表了。

然后再次执行`adb bugreport > out.txt`就会产生一个设备耗能统计信息文件。

### 注意

导出电量统计数据是一个持续输出的过程，就是说如果你不手动停止，那会一直统计。

所以看到文件中已经写入内容后就可以CTRL+C了。

# 数据可视化

我们得到的是一个可读性很差的txt文件，接下来就是把out.txt文件转为方便我们可视化的图表信息html

**老版本**使用命令`python historian.py out.txt > out.html`，其中**historian.py**在我们前言中下载的项目中scripts目录下。

转换后的页面如图：

![historian01](http://othg5ggzi.bkt.clouddn.com/historian-01.png)

![historian02](http://othg5ggzi.bkt.clouddn.com/historian-02.png)

![historian03](http://othg5ggzi.bkt.clouddn.com/historian-03.png)



**注意：刚打开html时可能页面只显示Battery Historian analysis for bugreport.txt，看不到其他内容，是因为页面正在分析txt，耐心等待下，不要关闭页面。**

图中垂直白线每个间隔在我这里表示半分钟，

battery_level（ 剩余电量）
鼠标悬停在 battery_level 变化处，显示剩余电量，以及距上次 battery_level 变化的间隔

plugged（连接）
电量来源（类似于上面的电量信息）。

screen（屏幕）
屏幕开启的时长。

top（上栏）
列举了当前屏幕上显示的进程

sync（同步）
处理与服务器的同步。

wake_lock

running（运行）
游戏时，电话一直在后台运行。

 wake_lock_in（唤醒锁）
可以看到哪些进程正在运行，以及所引起的 WakeLock 或 Alarm。在这张截图中，游
戏音频控制进程多次调用 WakeLock（ 播放不同的示例音乐引发了将近 2000 次音频
WakeLock）。同时也可以看到屏幕 WakeLock。

Job	后台的工作，比如服务service的运行

wifi

wifi_full_lock

wifi_scan（WIFI扫描）

wifi_running（WIFI运行状态）

我这里一条全是绿，说明一直在连接WIFI使用中



wifi_suppl（ Wi-Fi状态）


wifi_signal_strength（ Wi-Fi信号强度）
图 3-8 中，有 Wi-Fi 信号被检测到——尽管 Wi-Fi 已经被关闭，但由于高级 Wi-Fi 设置，
也会一直搜索 Wi-Fi 信号。

phone_signal_strength（无线网络信息 ）
显示信号变化（有差、中、好 三种）。

audio

fg
这里指的是前台应用。前台程序很少被销毁来释放内存。即使关闭屏幕，我们也可以看
到 Facebook 客户端在前台处理收到的信息。

status（状态）
正在放电（与充电状态相对应）。

health（健康）
电池健康状态，来自电池管理器 API。

plug（连接状态）
设备是否接通电源。



**通过上面的数据展示可以看出各个硬件耗电信息，根据相关信息和自己APP的相关设置，看哪些是不该出现的硬件使用而执行了，那这就是该优化的地方。比如在wake_lock_in横条中，左半部分红色竖条还是比较均匀的，但是到了右半部分，红条多了很多而且同一时间有多个wakelock发生了，这些都是需要的吗？不是的话就要想办法优化了。**

# 结尾

这里使用的一直是Battery Historian1.0，新版本的由于是GO语言重新的，所以需要安装Go的环境，有时间在尝试下。

新版本内容也更丰富，页面有四个标签：SystemStats（系统统计）、 Historian 2.0、 Historian（ legacy） 和 App Stats（应用程序统计）。