---
title: Java知识点
date: 2017-06-30 18:51:23
tags: Java
categories: Java
---

# 字符串

1、==对基本数据类型来说比较的是值是否相等，但对于引用类型来说，其和equals医院，比较的都是对象地址，前提是equals没有被重写。

```java
		String c = "c";
		final String c1 = "c";
		String s0 = "a" + "b" + c1;
		String s1 = "a" + "b" + c;
		String s2 = "a" + "b" + "c";
		String s3 = "abc";
		System.out.println(s0 == s3);//true
		System.out.println(s1 == s3);//false
		System.out.println(s2 == s3);//true
```

根据Java编译时优化方案，s2中加号的三个值都为固定常量，所以s2在编译时也被认为是常量，即编译期就确定了s2的值，并且和s3一样，所以打印true

在s1中，a和b的值是常量，但是c属于局部变量，而且也没有谁指定c这个值是不可变的。既然是可变量，导致s1也被编译期认为是不确定值，

接下来s0，a和b不用说了都是常量，而c1虽然和c一样也是局部变量，但是c1有finla修饰，明确告诉编译器明面上我是一个局部变量，但是同时我的值是不可变的，final修饰了，现在不变，将来也不会改变，所以编译器在编译期确定了s0 的值。所以把s0也作为常量和s3一样，都在常量池

***编译器优化要在编译期能确定的值得情况下进行，而能确定值的只能是在常量池中的内容。***

2、string.intern()，当字符串调用这个方法时，都会拿着当前字符串的值去常量池中找，如果找到则返回常量池这个常量地址，否则在常量池创建一个常量并把字符串填进去，然后返回创建的地址。当然这是在JDK1.6情况下，在1.7及以后会有不同

```java
		String c = "c";
		final String c1 = "c";
		String s0 = "a" + "b" + c1;
		String s1 = "a" + "b" + c;
		String s2 = "a" + "b" + "c";
		String s3 = "abc";
		System.out.println(s0 == s3);
		System.out.println(s1.intern() == s3);//true
		System.out.println(s2 == s3);
```

***由于intern()需要去常量池中做字符串比较，而常量池又很可能有多个常量，所以一般来说intern()效率并不高***







