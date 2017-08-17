---
title: 'ascii' codec can't decode byte 0xe4 in position 0: ordinal not in range(128)
date: 2017-08-18 02:57:25
tags: Python
categories: Python
---

# 0x00 前言

Mac里面装了python2.7和python3.6两个版本。

但是Scrapy默认使用的是2.7版本，所以遇到了编码问题：

**'ascii' codec can't decode byte 0xe4 in position 0: ordinal not in range(128)**

# 0x01 解决

## 只修改项目文件

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



## 修改全局编码环境

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