---
title: Python中_和__有什么意义?
date: 2017-08-08 22:35:40
tags: Python
categories: Python
---

# 前言

python中很多源码都是由单下划线和双下划线开头的函数、变量、模块。

但是一直不太明白这表示什么意思。然后因为好奇就查了下资料才才明白怎么回事。



# 了解

## 单下划线（_）

以**单下划线**开头的名字都应该是内部实现.

函数以单下划线开头说明这是一个特殊函数或者认为是一个隐藏函数，作为一个内部方法，只能被本模块代码使用。

Python没有强制依赖于语言特性去达到封装数据目的，而是通过归约。而这里的下划线就是归约。

> Python 并不会真的阻止别人访问内部名称。但是如果你这么做肯定是不好的，可能会导致脆弱的代码。同时还要注意到，使用下划线开头的约定同样适用于模块名和模块级别函数。



## 双下划线（__）

使用双下划线开始会导致访问名称变成其他形式。这种以双下划线开头的函数不会被通过类继承覆盖。

Python文档指出，“__spam”这种形式（至少两个前导下划线，最多一个后续下划线）的任何标识符将会被“_classname__spam”这种形式原文取代，在这里“classname”是去掉前导下划线的当前类名。

```python
class B:
    def __init__(self):
        self.__private = 0

    def __private_method(self):
        print('b___private_method')


    def public_method(self):
        self.__private_method()

    def getPrivateValue(self):
        return self.__private

class C(B):
    def __init__(self):
        super().__init__()
        self.__private = 1

    def __private_method(self):
        print('c___private_method')

    def getPrivateValue(self):
        return self.__private


c = C()
c.public_method()
# print(c.getPrivateValue())

```

上面代码执行后结果如下：

```
b___private_method

Process finished with exit code 0
```

可以看到打印的时B类中方法，而不是C类方法，说明虽然这两个方法方法名一样，但是没有被重写。

## 前后双下划线

这种用法表示Python中特殊的方法名。其实，这只是一种惯例，对Python系统来说，这将确保不会与用户自定义的名称冲突。通常，你将会覆写这些方法，并在里面实现你所需要的功能，以便Python调用它们。例如，当定义一个类时，你经常会覆写“__init__”方法。

