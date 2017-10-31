---
title: django时区显示不正确比本地时间小8小时
date: 2017-10-31 12:28:15
tags: Django
categories: Django
---



页面显示时间一直比本地时间小8小时，明显是时区设置有问题。

时区的设置在**settings.py**中：

```python
# TIME_ZONE = 'UTC'时区会比本地小8个小时
TIME_ZONE = 'Asia/Shanghai'
```

把UTC时区设置为**Asia/Shanghai**刷新页面后时间恢复正常。