---
title: 关于Android魅族不重要通知
date: 2017-09-21 15:36:57
tags: Android
categories: Android
---

# 0x00 前言

魅族不重要通知是什么鬼？

<!-- more -->

先直接看图：

![不重要通知](http://othg5ggzi.bkt.clouddn.com/%E4%B8%8D%E9%87%8D%E8%A6%81%E9%80%9A%E7%9F%A5.jpg)



![一般通知](http://othg5ggzi.bkt.clouddn.com/%E4%B8%80%E8%88%AC%E9%80%9A%E7%9F%A5.jpg)



通过这两个通知页面就能看到问题了，有些Rom厂商根据通知优先级设置了不同的显示页面。这有每次消通知时候要消两次，所以就好奇这个优先级是不是代码里的那个优先级设置。

# 0x01 通知优先级

在android通知中重要性可以分为5个优先级：

```java
    public static final int PRIORITY_DEFAULT = 0;
    public static final int PRIORITY_LOW = -1;
    public static final int PRIORITY_MIN = -2;
    public static final int PRIORITY_HIGH = 1;
    public static final int PRIORITY_MAX = 2;

```

既然都提到了优先级，所以自然就以为魅族只是根据这个值的不同来显示在不同的页面，把LOW和MIN显示在不重要通知页面了。

赶紧写个demo测试下假想：

```java
    @Override
    public void onClick(View v) {
        switch (v.getId()) {
            case R.id.send_btn_normal:
                builder.setContentTitle("这是一条默认通知").setSmallIcon(R.drawable.ic_launcher)
                        .setContentText("这是内容");
                mNotificationManager.notify(R.id.send_btn_normal, builder.build());
                break;
            case R.id.send_btn_high:
                builder.setContentTitle("这是一条high通知").setContentText("这是内容").setSmallIcon(R.drawable.ic_launcher).
                        setPriority(NotificationCompat.PRIORITY_HIGH);
                mNotificationManager.notify(R.id.send_btn_high, builder.build());
                break;
            case R.id.send_btn_low:
                builder.setContentTitle("这是一条low通知").setContentText("这是内容").setSmallIcon(R.drawable.ic_launcher).
                        setPriority(NotificationCompat.PRIORITY_LOW);
                mNotificationManager.notify(R.id.send_btn_low, builder.build());
                break;
            case R.id.send_btn_min:
                builder.setContentTitle("这是一条min通知").setContentText("这是内容").setSmallIcon(R.drawable.ic_launcher).
                        setPriority(NotificationCompat.PRIORITY_MIN);
                mNotificationManager.notify(R.id.send_btn_min, builder.build());
                break;
            case R.id.send_btn_max:
                builder.setContentTitle("这是一条max通知").setContentText("这是内容").setSmallIcon(R.drawable.ic_launcher).
                        setPriority(NotificationCompat.PRIORITY_MAX);
                mNotificationManager.notify(R.id.send_btn_max, builder.build());
                break;
        }
    }
```

上面是点击发送通知的部分代码，发送完通知再看看显示结果：

![通知测试](http://othg5ggzi.bkt.clouddn.com/%E9%80%9A%E7%9F%A5%E6%B5%8B%E8%AF%95.jpg)



测试发现不同优先级的通知都还在默认的通知界面。并没有按假想的那样把LOW和MIN放到不重要通知！但是有一点不同的是通知顺序和点击的顺序不一样。所以**通知优先级只是改变了通知显示顺序**

那么魅族是如何区分通知是否重要呢？

网上查了下资料发现了真相：

>具体的触发就是，如果你想把垃圾的信息放到盒子里面，就是当手机状态栏弹出了消息以后，你选择右滑动删除消息，千万不要点击进入，是直接右滑动删除消息，当时连续几次这样操作以后，系统就会把以后类似的消息放到消息盒子里面了。 同理如果你希望把已经在消息盒子里面的消息独立出来，当看到消息盒子里面的信息出现后，你直接点击消息进入，连续操作几次以后就会从消息盒子里面出来了哈。希望对还在困扰的你们有帮助，一个一个字打出来的，觉得有用的就支持下吧。



这个号称智能通知栏的家伙居然只靠滑动和点击就果断的给你归为是否重要，而且还说智能学习。真是有点名不副实了。

另外还要吐槽一下**淘票票**，我特么的猩球崛起3都已经用这个APP买票都已经看了，还在给我推消息！！

