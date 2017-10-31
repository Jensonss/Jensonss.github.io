---
title: django日期格式化显示
date: 2017-10-31 12:28:39
tags: Django
categories: Django
---

页面显示日期格式一直是`Oct. 30, 2017, 2 p.m`这种格式。显然看起来并不舒服。

有没有什么办法给格式化成**Y-m-d H-m**的格式呢？

Django提供了过滤器可以格式化date

```python
{{ post.created_time|date:"Y-m-d H:i" }}
```



