---
title: Android各版本新功能及区别
date: 2017-07-26 13:17:15
tags: Android
categories: Android
---

#  0x00 前言

Android版本更新快，版本越来越越多，兼容性也就要越来越注意，注意各个版本新加了哪些功能，哪些功能和API对开发有帮助，都要心里有个数



# 0x01 Android8.0 

waiting...



# 0x02 Android 7.0 Nougat

- 引入了一个新的而且非常需要的多任务处理功能 — 多窗口支持。

- **通知增强** 通知界面直接回复等

- 进一步的低耗电模式Doze 增强版

- 删除了三个常用隐式广播 — `CONNECTIVITY_ACTION`、`ACTION_NEW_PICTURE` 和`ACTION_NEW_VIDEO` 

  ​

# 0x03 Android 6.0 Marshmallow

- **引入了一种新的权限模式 运行时权限检查**

  对于以 Android 6.0（API 级别 23）或更高版本为目标平台的应用，请务必在运行时检查和请求权限。要确定您的应用是否已被授予权限，请调用新增的 `checkSelfPermission()` 方法。要请求权限，请调用新增的 `requestPermissions()` 方法。即使您的应用并不以 Android 6.0（API 级别 23）为目标平台，您也应该在新权限模式下测试您的应用。

- 取消支持 Apache HTTP 客户端

  Android 6.0 版移除了对 Apache HTTP 客户端的支持。如果您的应用使用该客户端，并以 Android 2.3（API 级别 9）或更高版本为目标平台，请改用 `HttpURLConnection` 类。此 API 效率更高，因为它可以通过透明压缩和响应缓存减少网络使用，并可最大限度降低耗电量。要继续使用 Apache HTTP API，您必须先在 `build.gradle`文件中声明以下编译时依赖项：

  ```
  android {
      useLibrary 'org.apache.http.legacy'
  }
  ```

- **低电耗模式和应用待机模式：Doze模式**

- **新的通知构建方法**

  移除了 `Notification.setLatestEventInfo()` 方法。请改用 `Notification.Builder` 类来构建通知。要重复更新通知，请重复使用 `Notification.Builder` 实例。调用 `build()` 方法可获取更新后的 `Notification` 实例。

  `adb shell dumpsys notification` 命令不再打印输出您的通知文本。请改用 `adb shell dumpsys notification --noredact` 命令打印输出 notification 对象中的文本。

- **Android 密钥库变更**

  从此版本开始，[Android 密钥库提供程序](https://developer.android.com/training/articles/keystore.html)不再支持 DSA。但仍支持 ECDSA。

  停用或重置安全锁定屏幕时（例如，由用户或设备管理员执行此类操作时），系统将不再删除需要闲时加密的密钥，但在上述事件期间会删除需要闲时加密的密钥。

  ​

# 0x04 Android 5.0 Lollipop

- **引入了Material Design并提供了UI工具包**

- **把ART模式作为默认的运行模式**

- Android 5.0 中的通知更醒目、更便于访问并且可配置程度更高。

- Android 5.0 还引入了 Android 扩展包 (AEP)，支持 OpenGL ES 3.1

- Android 5.0 引入了全新的 Camera API

- 在 Android 5.0 中，新增的倾斜检测器传感器有助于改善受支持设备上的 Activity 识别

- 新增的 **Job Scheduling** API 允许通过将作业推迟到稍后或指定条件下（如设备充电或连入 WLAN 时）运行来优化电池寿命。

  新增的 `dumpsys batterystats` 命令可生成**电池使用情况统计信息**，可通过它了解整个系统的耗电情况，以及了解应用对设备电池的影响。



# 0x05 Android 4.4 Jelly Bean

- Android 4.4 针对硬件传感器批处理引入平台支持

- **全屏沉浸模式**

  Android 4.4 添加了新的全屏沉浸模式，让您可以在手机和平板电脑上创建从一个边缘延伸到另一个边缘的全出血 UI，**隐藏所有系统 UI**，例如状态栏和导航栏。它适用于鲜艳的视觉内容，例如照片、视频、地图、书本和游戏。

  在新模式中，系统 UI 保持隐藏状态，即使在用户与您的应用或游戏交互时 - 您可以从屏幕上的任何位置（甚至在系统栏占据的其他位置）捕获触摸事件。这是您在应用或游戏中创建更大、更加丰富和更加沉浸式的 UI 并减少视觉分散的绝佳方式。

  为确保用户在全屏沉浸模式中始终能轻松、一致地访问系统 UI， Android 4.4 支持新的手势。现在，在沉浸模式中，从屏幕顶部或底部边缘滑动手指可以显示系统 UI。

  要返回沉浸模式，用户可以触摸栏界限之外的屏幕或等待一小段时间，让栏自动隐藏。为提供一致的用户体验，新的手势还与之前隐藏状态栏的方法配合使用。

- 安全增强功能

  SELinux（强制模式）
  Android 4.4 将其 SELinux 配置从“permissive”更新为“enforcing”。这意味着，在具有强制政策的 SELinux 域中，将阻止潜在的政策违规行为。

  改进加密算法
  Android 添加了对另外两种加密算法的支持，进一步提升了其安全性。已向密钥库提供程序添加椭圆曲线数字签名算法 (ECDSA) 支持，从而提升了数字签名的安全性，适用于应用签名或数据连接等场景。实现 Scrypt 密钥派生函数，以保护用于全磁盘加密的加密密钥。

- 内存使用率分析工具procstats

- Android 4.4 包含新的开发者选项：设备内存状态和分析

  Settings > Developer options > Process stats

- **可重复使用的位图，参考[inBitmap](https://developer.android.com/reference/android/graphics/BitmapFactory.Options.html#inBitmap)**