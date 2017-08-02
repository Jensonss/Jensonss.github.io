---
title: Mac系统中希捷移动硬盘NTFS格式只能读
date: 2017-08-01 01:03:05
tags: [Mac, 工具]
categories: [Mac, 工具]
---

# 前言

今天Mac系统更新完成后，希捷移动硬盘突然只能读不能写了。





# 解决

Mac默认不支持NTFS格式，所以要想兼容需要安装[Paragon驱动](http://www.seagate.com/cn/zh/support/downloads/item/ntfs-driver-for-mac-os-master-dl/)。

- 在开始安装前，确保硬盘连接至电脑。
- 双击您下载的 **NTFS_for_Mac_14.0.456.dmg** 文件。
- 按照屏幕提示完成安装。
- 重启后即可读写硬盘