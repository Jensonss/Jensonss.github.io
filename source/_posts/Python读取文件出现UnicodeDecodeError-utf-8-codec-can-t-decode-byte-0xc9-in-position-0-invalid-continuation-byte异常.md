---
title: >-
  Python读取文件出现UnicodeDecodeError: 'utf-8' codec can't decode byte 0xc9 in
  position 0: invalid continuation byte异常
date: 2017-07-29 19:00:48
tags: Python
categories: Python
---

# 前言

在用python读取文本时出现了如下异常：

`UnicodeDecodeError-utf-8-codec-can-t-decode-byte-0xc9-in-position-0-invalid-continuation-byte`。



在读取的时候注意统一编码，因为文本文档设置的时GBK，

所以读取的时候也要设置GBK，而没有设置时默认使用的UTF-8。

```python
text_from_file_with_apath = open('/Users/jenson/Downloads/炮灰修仙记事.txt',encoding='GBK').read()

```

**读取时设置统一编码即可**



