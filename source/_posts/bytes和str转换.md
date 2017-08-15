---
title: bytes和str转换
date: 2017-08-13 16:27:54
tags: Python
categories: Python
---

# 前言

日常开发中很多时候要用到bytes和str的互相转换，每次都查浪费时间。干脆整理一次记在脑子里。

# bytes转str

- 默认解码 bytes.decode()
- gbk方式解码 bytes.decode(‘gbk’)

# str转bytes

- utf8方式编码 str.encode(encoding="utf-8")
- gbk方式编码 str.encode(encoding="gbk")