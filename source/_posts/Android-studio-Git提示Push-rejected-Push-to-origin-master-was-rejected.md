---
title: 'Android studio Git提示Push rejected: Push to origin/master was rejected'
date: 2017-08-25 13:27:03
tags: Android
categories: Android
---

# 0x00 前言

最近做一个IPTV项目，由于经常和历史记录做对比或者实现新思路不行而还原，所以要把项目添加到Git。

但是当用Android studio push的时候出现异常信息：

```
Push rejected: Push to origin/master was rejected
Git Pull Failed: fatal: refusing to merge unrelated histories

```

因为刚创建的Git remote仓库初始化时顺带创建了一个read.me文件，

而本地已经add过了。

<!-- more -->

# 0x01 解决

因为Android studio可以执行的命令有限（或者还未发现新天地），所以cd到项目目录执行

```
git pull origin master --allow-unrelated-histories
```

即可。