---
title: Python文件编码问题
date: 2017-08-18 17:08:04
tags: Python
categories: Python
---
# 0x00 前言

python读取文件时提示"UnicodeDecodeError: 'gbk' codec can't decode byte 0x80 in position 205: illegal multibyte sequence"

# 0x01 解决

## 解决办法1.

FILE_OBJECT= open('order.log','r', encoding='UTF-8')

## 解决办法2.

FILE_OBJECT= open('order.log','rb')


