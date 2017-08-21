---
title: PhantomJS运行提示：Message 'phantomjs' executable needs to be in PATH？
date: 2017-08-20 11:08:04
tags: Python
categories: Python
---

# 0x00 前言

使用selenuim调用PhantomJS时一直提示异常：

Message 'phantomjs' executable needs to be in PATH？



# 0x01 解决

调用代码如下：

```python
driver = webdriver.PhantomJS(executable_path="D:\jenson\phantomjs\\bin")

```

路径只写到bin是不行的，要具体到文件

```python
driver = webdriver.PhantomJS(executable_path="D:\jenson\phantomjs\\bin\phantomjs.exe")

```

