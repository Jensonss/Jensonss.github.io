---
title: Android本地广播
date: 2017-09-14 17:50:05
tags: Android
categories: Android
---

# 0x00 前言

在android开发中广播作为四大组件之一，其重要性不言而喻。虽然重要但使用却是极其简单，

通常我们的使用无非就是`sendBroadCast`和继承BroadCastReceiver重写`onReceive`方法。

如果多想想其实这种做法是存在安全隐患的，比如我们发送的广播是全局性的，那么有被其他程序截获泄露的风险，同样的我们广播接收器也是全局性的，垃圾应用也有可能不停发送垃圾广播。

对于这种安全隐患如何避免呢？

**Google为我们提供了本地广播服务：`LocalBroadcastManager`对广播进行管理。让我们发出的广播只在应用内传递，且广播接收器也只接收来自本应用的广播。**

# 0x01 使用

`LocalBroadcastManager`在SDK22版本添加在v4包中。



## 实例化

```java
private LocalBroadcastManager manager;

manager.getInstance(Context context)
```



## 本地发送广播

```java
private LocalBroadcastManager manager;

manager.sendBroadcast(intent);
```



## 本地注册广播

```java
private LocalBroadcastManager manager;

manager.registerReceiver(receiver,filter)
```



## 本地取消广播

```java
private LocalBroadcastManager manager;

manager.unregisterReceiver(receiver)
```





