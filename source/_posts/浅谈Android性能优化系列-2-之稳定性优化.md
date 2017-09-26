---
title: 浅谈Android性能优化系列(2)之稳定性优化
date: 2017-09-26 17:47:22
tags: Android
categories: Android
---

# 0x00 前言

上一节讲了安装包优化，本节说下稳定性优化方面入手点。

相比于电量和网络，稳定性可视性更高，什么是可视性？就是用户的感知程度！

电量多耗了百分之几和网络流量多跑了几兆，可能用户并不会注意到，但是一旦发生ANR或者Crash，用户基本是必感知的，而且每一次的感知都会降低对你的APP的忍耐度，一旦忍耐度耗光，你的APP也就要该卸载了。

虽然稳定性很重要，但是稳定性优化还是很简单的（相对其他几个方面来说）。

# 0x01 稳定性优化

## 避免ANR

什么是ANR？Application Not Response，应用无响应。

要解决ANR，首先要知道什么原因引起的ANR才好入手：

- 输入事件5s未完成
- 广播10s未完成
- 服务20s未完成

**其实这几个方面的根本原因就是在主线程做了耗时操作，在处理ANR时可以使用traceview查看方法耗时以便修复。**

<!--more-->

## 良好异常捕获机制

人无完人，同样我们也不可能一次写出完美的程序。如果异常不能避免，那我们除了做好必要的减少异常措施，还要实现一套良好的异常捕获机制，以便研发人员及时收集异常信息进行修复。

关于未知异常捕获，Android系统为我们提供了一个类：`UncaughtExceptionHandler`。

```java
package com.example.jenson.myapplication;

import android.content.Context;
import android.os.Build;
/**
 * Created by jenson on 2017/9/26.
 */

public class CrashHandler implements Thread.UncaughtExceptionHandler {
    private Thread.UncaughtExceptionHandler mDefaultHandler;
    private static CrashHandler mInstance;
    private Context mContext;

    private CrashHandler() {
    }

    /**
     * 获取CrashHandler实例
     */
    public static synchronized CrashHandler getInstance() {
        if (null == mInstance) {
            mInstance = new CrashHandler();
        }
        return mInstance;
    }

    public void init(Context context) {
        mContext = context;
        mDefaultHandler = Thread.getDefaultUncaughtExceptionHandler();
        //设置该CrashHandler为系统默认的
        Thread.setDefaultUncaughtExceptionHandler(this);
    }


    @Override
    public void uncaughtException(Thread t, Throwable e) {
        if (!handleException(e) && mDefaultHandler != null) {
            //如果自己没处理交给系统处理
            mDefaultHandler.uncaughtException(t, e);
        } else {
            //自己处理
        }

    }

    /**
     * 收集错误信息.发送到服务器
     *
     * @return 处理了该异常返回true, 否则false
     */
    private boolean handleException(Throwable ex) {
        if (ex == null) {
            return false;
        }
        //收集设备参数信息
        //发送到服务器
        return true;
    }
}

```

在Application中调用`CrashHandler.getInstance().init(this)`。



## 代码审查

代码审查可以借助于工具，也可以同事之间。

如果是同事之间可以团队审查、结对审查。

团队审查由组长对组员提交进行审查，结对审查同事之间两两互相审查。

代码审查到底审查什么？

参考http://www.techug.com/post/code-review-2.html

