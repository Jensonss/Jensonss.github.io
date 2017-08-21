---
title: Python在Windows下提示ModuleNotFoundError No module named win32api
date: 2017-08-20 11:08:04
tags: Python
categories: Python
---

# 0x00 前言

在windows下运行python时提示该异常信息。



# 0x01 解决

- 使用字符串函数replace

  ```python
  a = 'hello world'
  a.replace(' ', '')
  'helloworld'
  ```

  ​

- 使用字符串函数split

  ```python
  a = ''.join(a.split())
  print(a)
  helloworld
  ```

  ​

- 使用正则表达式

  ```python
  import re
  strinfo = re.compile()
  strinfo = re.compile(' ')
  b = strinfo.sub('', a)
  print(b)
  helloworld
  ```

  ​