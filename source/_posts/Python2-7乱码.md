---
title: Python2.7编码异常问题
date: 2017-08-18 02:57:25
tags: Python
categories: Python
---

# 0x00 前言

Mac里面装了python2.7和python3.6两个版本。

但是Scrapy默认使用的是2.7版本，所以遇到了编码问题：

# 'ascii' codec can't decode byte 0xe4 in position 0: ordinal not in range(128)

## 0x01 解决

### 只修改项目文件

在文件前加上如下代码：

```python
import sys
defaultencoding = 'utf-8'
if sys.getdefaultencoding() != defaultencoding:
    reload(sys)
    sys.setdefaultencoding(defaultencoding)
```

> 这种情况有个问题就是如果系统默认环境是2.7，而开发工具是3.6，此时如果你的项目创建时使用默认环境2.7创建，但是确使用3.6版本IDE来开发，这样就导致**reload**不能被识别。

所以推荐方法二：修改全局编码环境

<!-- more -->

### 修改全局编码环境

Mac中有两处sitepackage，找到目录：

```
/usr/local/lib/python2.7/site-packages
/Library/Python/2.7/site-packages
```

在目录下创建文件：**sitecustomize.py**，添加内容：

```python
import sys
sys.setdefaultencoding('utf-8')
```

 这种方式可以解决所有项目的encoding问题，具体说明可参考/usr/lib/python2.7/site.py文件：

# SyntaxError: Non-ASCII character '\xe7' in file  

### 0x01 解决

这个异常是因为在2.7版本中，python源文件中出现了ASCII以外格式编码，我这是当然就是出现中文了，

只要在源文件顶部添加编码注释即可：

```python
#-*- coding: UTF-8 -*-   
```



# ValueError: All strings must be XML compatible: Unicode or ASCII, no NULL bytes or control characters

### 0x00 前言

这是在使用xpath解析时，路径中带了中文导致的，版本仍然为2.7

### 0x01 解决

异常信息已经告诉我们了，字符串只能是unicode或者ascii编码。

把xpath加u即可：

```python
recommend_count = div.xpath(query=u"./p/span[@title='推荐']/text()")[0].extract()

```

