---
title: typeerror module.__init__() takes at most 2 arguments (3 given)
date: 2017-07-31 20:21:33
tags: Python
categories: Python
---

#  前言

代码如下：

```python
from scrapy.selector import Selector
from scrapy import spiders , item
from scrapy.utils import response

from daobanSpider.items import Article


class articleSpider(spiders):
    name = "article"
    allowed_domains = ["en.wikipedia.org"]
    start_urls = ["http://en.wikipedia.org/wiki/Main_Page",
                  "http://en.wikipedia.org/wiki/Python_%28programming_language%29"]


def parse(self, response):
    item = Article()
    title = response.xpath('//h1/text()')[0].extract()
    print("Title is: " + title)
    item['title'] = title
    return item
```

`class articleSpider(spiders):`这一行出现异常，信息如下：

TypeError: module.__init__() takes at most 2 arguments (3 given)





# 解决

我这是定义的一个class继承spider。但是这里提示类型错误，竟然还出现了module.__init__() 。。

查找后才明白过来`from scrapy import spiders`导入的spiders不是类，而是从scrapy框架导入spiders模块，如要使用某些类，需要再通过module.class格式引入

所以异常的那一行改为：`class articleSpider(spiders.CrawlSpider):`。





