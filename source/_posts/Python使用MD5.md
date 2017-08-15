---
title: Python使用MD5
date: 2017-08-13 17:17:54
tags: Python
categories: Python
---

# 前言

最近接口测试时用到了md5加密，接口是给app端调用的，但是用java做比较麻烦。所以干脆使用Python来调试好了。

# 使用

## 方法一：使用`from hashlib import md5`模块

```python
 md5_ = md5()
 md5_.update((mac + as_token).encode('utf-8'))
 print(md5_.hexdigest())
```

**这个update方法需要编码后传入，否则抛异常**

## 方法二：直接使用`import md5`模块

```python
import md5
m = md5.new()
s = "hahaha"
m.update(s)
print (m.degest())
print (m.hexdigest()) #返回结果
```

网上很多人说可以使用这个模块，但是在我的Python3.6版本中，md5这个模块变成了保留模块。

所以推荐使用方法一。