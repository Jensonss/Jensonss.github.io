---
title: Python操作xml
date: 2017-08-13 16:30:54
tags: Python
categories: Python
---

# 前言

xml和json是客户端和服务端进行数据交互的常用格式。所以掌握这些数据格式的解析是很必要的。python提供了两种xml解析方法。一种是常见的行业标准做法：sax和dom解析，另一种是python特有的解析方法，而在标准库中ElementTree首先推荐。

# ElementTree模块

## 导入模块

```python
from xml.etree.ElementTree import ElementTree, parse as eleParse, Element
from urllib import request, parse
```

## 开始解析

通过数据源，创建**ElementTree**。这里使用模块中的**parse**方法。

```python
doc = parse(source=source)
```

**这里的参数source是一个文件名或者包含xml数据的文件对象。**

## 常用方法

`doc.findtext('username')`返回指定标签的元素文本

`doc.iterfind('category')`返回所有匹配子元素

`doc.findall(path)`返回所有匹配子元素

`item.attrib['id']`返回元素属性，这里的item是Element对象



# DOM模块





# SAX模块

