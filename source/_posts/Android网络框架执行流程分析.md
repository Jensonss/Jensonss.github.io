---
title: Android网络框架okhttp执行流程分析
date: 2017-05-10 00:58:46
tags: Android
categories: Android
---

# 说在前面的话

Android开发中，网络框架okhttp可以说是越来越风光，Android4.4的源码中可以看到HttpURLConnection已经替换成OkHttp，能够被谷歌认可，足见okhttp的强大之处了。既如此还有什么理由不去深入理解下它呢？很惭愧，这么好的东西以前一直处于简单调用，今天有时间把流程看了一下，简单总结了下执行请求时的大概流程作为备忘。

<!-- more -->

# 执行流程

![流程](Android网络框架执行流程分析/流程.png)



