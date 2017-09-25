---
title: Python入门系列(2):字符串、变量和简单数据类型的上手
date: 2017-04-22 18:55:54
tags: Python
categories: Python
---

# 说在前面的话

java在声明变量时都要先指定数据类型 比如 int a=3 ; Stringt name = "jenson";
但是在python中可以直接使用变量
python中的注释使用"#"

<!-- more -->

# 字符串的使用

- 直接声明变量

```
print("jenson")
name ="jenson"
print(name)
```

打印如下

```
jenson
jenson
[Finished in 0.0s]
```
- 字符串拼接直接使用+号

```
print("hello jenson")
name = "hello"+ " jenson"
print(name)
```

打印结果一样一样的

```
hello jenson
hello jenson
[Finished in 0.0s]
```

- 去除字符串的空白

```
name = "   jenson   "
print(name.lstrip())
print(name.rstrip())
print(name.strip())
```

结果如下

![屏幕快照 2017-01-06 下午10.01.19.png](http://upload-images.jianshu.io/upload_images/1796052-c10ea4a7be3c8130.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这里去除的空白分别是左边、右边和两边的空白，并不能去除中间空白
- 字符串的大小写

```
say = "hello jenson"
print(say)
print(say.upper())
print(say.lower())
print(say.title())
```

结果如下：

```
hello jenson
HELLO JENSON
hello jenson
Hello Jenson
[Finished in 0.0s]
```

可见三个函数的作用分别是使字符串大写、小写、单词首字母大写。
- 基本类型和运算

```
print(3+5)
print(10-2)
print(2*4)
print(16/2)
print(2+3*4)
print(2**3)
```

输出如下：

```
8
8
8
8.0
14
8
[Finished in 0.2s]

```
可以看出python对四则运算支持优先级，注意一下*这里的幂用2个乘号表示*
- 基本类型转为字符串
  基本类型和字符串混合使用时注意把基本类型先转换为字符串，使用str(基本类型值)，否则会引发异常。
```
age = 25;
print(str(age))
say = "I'am "+age 
print(say)
```

```
25
Traceback (most recent call last):
  File "/Users/jenson/Desktop/python_work/str_text.py", line 15, in <module>
    say = "I'am "+age 
TypeError: Can't convert 'int' object to str implicitly
[Finished in 0.0s with exit code 1]
[cmd: ['/Library/Frameworks/Python.framework/Versions/3.5/bin/python3', '-u', '/Users/jenson/Desktop/python_work/str_text.py']]
[dir: /Users/jenson/Desktop/python_work]
[path: /usr/bin:/bin:/usr/sbin:/sbin]
```

第一个print已经打印，第二个却报了异常，因为没有转换。

- python2和python3整数除法比较

在python2下做除法运算3/2，得到结果是1 ，并不是1.5

![屏幕快照 2017-01-06 下午10.34.19.png](http://upload-images.jianshu.io/upload_images/1796052-92eac21fee40ffd9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

而在python3下是正常的

![屏幕快照 2017-01-06 下午10.35.27.png](http://upload-images.jianshu.io/upload_images/1796052-4b8c5bd6f9354a2e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这是因为python2中计算整数结果时不是采取四舍五入，而是将整数部分直接删除，所以在python2中如果要避免这种情况，要确保至少有一个操作数为浮点数，这样得到的结果也为浮点数

完毕。