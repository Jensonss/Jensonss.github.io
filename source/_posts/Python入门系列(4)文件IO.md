---
title: Python入门系列(4):文件IO
date: 2017-08-06 17:25:54
tags: Python
categories: Python
---

# 前言

文件读写是最常见的IO操作，而python也内置了文件读写的函数，下面看看如何读写文件。

# 操作

## mode

不论读还是写，只要想操作文件就要先打开文件，而打开文件时要告诉系统你的mode是什么？

什么是mode？就是你打算对这个文件操作的类型，是读还是写，如果是写是覆盖还是追加？

下面表格列出了mode值及含义：

|  值   |  描述   |
| :--: | :---: |
| 'r'  |  读模式  |
| 'w'  |  写模式  |
| 'a'  | 追加模式  |
| 'b'  | 二进制模式 |
| '+'  | 读/写模式 |

**其中b和+可以和前面模式混用**。

打开文件使用**open***函数：

```python
file= open(r"file\t.txt", mode='a+')
```

>open函数中第三个可选参数buffering控制着⽂件的缓冲。 如果参数是0， I/O操作就是⽆缓冲的， 直接
>将数据写到硬盘上； 如果参数是1， I/O操作就是有缓冲的， 数据先写到内存⾥， 只有使⽤flush函数或者
>close函数才会将数据更新到硬盘； 如果参数为⼤于1的数字则代表缓冲区的⼤⼩（单位是字节） ， -1（或者
>是任何负数） 代表使⽤默认缓冲区的⼤⼩。 



## 读文件

文件读取使用read方法。可以一次读取全部、读取一行、读取指定长度

```python
print(file.read())
print(file.readline())
print(file.read(3))
```

## 写文件

写文件使用write方法。

```python
file.write('\ntoday is sunday')
str=['aa', 'bb', 'cc']
file.writelines(str)
```

## 异常处理

IO操作完成后都要有一个关闭操作对于文件来说，不使用时要关闭：

```python
file.close()
```

但是IO操作难免出现异常，如果在文件操作期间出现异常可能会导致无法关闭文件。这时需要异常处理机制：`try except`

但是异常处理机制又略显繁琐，so这里推荐使用**with**语法：

```python
with  open(r"file\t.txt", mode='a+') as file_object:
contents = file_object.read()
print(contents)
```

> 关键字with在不再需要访问文件后将其关闭。在这个程序中，注意到我们调用了open()，但
> 没有调用close()；你也可以调用open()和close()来打开和关闭文件，但这样做时，如果程序存
> 在bug，导致close()语句未执行，文件将不会关闭。这看似微不足道，但未妥善地关闭文件可能
> 会导致数据丢失或受损。如果在程序中过早地调用close()，你会发现需要使用文件时它已关闭
> （无法访问），这会导致更多的错误。并非在任何情况下都能轻松确定关闭文件的恰当时机，但通
> 过使用前面所示的结构，可让Python去确定：你只管打开文件，并在需要时使用它， Python自会
> 在合适的时候自动将其关闭 