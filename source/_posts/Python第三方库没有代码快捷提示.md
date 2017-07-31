---
title: Python第三方库没有代码快捷提示
date: 2017-08-01 00:45:00
tags: Python
categories: Python
---

# 前言

做Python开发IDE首选当然是Pycharm，但是有个问题是如果安装第三方库后，在使用的时候，第三方库返回的变量调用方法时没有代码快捷提示。



# 解决

其实是因为Python是弱类型编程语言。

像Java这种强类型语言，在声明变量时都要先声明变量类型。这种类型明确自然也就知道其实例所对应的方法，所以能给出提示。

而Python这种弱类型语言，使用时候只声明变量，不声明类型即可使用，

变量不知道当前引用所指向的类型是哪个，IDE自然也给不出快捷提示。

但是如果在代码中给出变量指定类型，那么IDE就可以给出提示。

那么问题来了，如何给变量指定类型？

- 单行注释指定类型 :

  `csv_writer = csv.writer(csvFile)  # type: csv.DictWriter`。

  **注意格式：#后面跟type:**

- 多行注释指定类型：

  ```python
  """
     :type: csv.DictWriter
  """
  ```

  **注意：这个多行注释中的type前须添加 : **

  ​

- 使用isinstance指定

  `assert isinstance(item, bs4.Tag)`。

  **注意：参数1表示变量，参数2表示指定类型**

  ​