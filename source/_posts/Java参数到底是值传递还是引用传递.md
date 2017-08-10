---
title: Java参数到底是值传递还是引用传递
date: 2017-08-10 13:55:54
tags: Java
categories: Java
---

# 0x00前言

基本类型是值传递，引用类型是引用传递，在此前很长的一段时间内，我一直是这么认为的！

然鹅，这个错误的想法竟然伴随多年，仿佛多年的Java白学了。



# 0x01 说明

**其实Java中参数的传递都是byValue方式传递**。

讲道理先通过虚拟机机制来说明下：

![讲道理](http://othg5ggzi.bkt.clouddn.com/%E8%AE%B2%E9%81%93%E7%90%86.jpg)



虚拟机在内存中有一块区域叫Java栈，也叫虚拟机栈。栈里面装的是什么？

栈中存放的是栈帧，栈帧里面又装的是什么？是局部变量表、操作数栈和方法返回地址等信息。

其实一个Java线程运行时就会产生一个Java栈，每次的方法调用就会产生一个栈帧入栈，方法执行完毕则出栈。其中栈帧中的局部变量表存储的是参数和局部变量信息。

当方法执行完毕，栈帧出栈内存就会被释放，如果引用类型的参数是直接把函数外的引用直接传递过来，那么出栈时，岂不是连函数外的这个变量也一同给释放掉了吗？

所以为了避免这种情况发生，**引用类型的参数传递，只是对引用类型值的一个拷贝。**

就是说**函数外的变量和参数是两个不同的变量，只是它们指向的地址是一样的**：

![参数引用类型传递](http://othg5ggzi.bkt.clouddn.com/%E5%8F%82%E6%95%B0%E5%BC%95%E7%94%A8%E7%B1%BB%E5%9E%8B%E4%BC%A0%E9%80%92.png)



说到底，引用类型也躲不过值传递的命运。

下面用示例来验证下外部变量和参数是两个不同的变量：

# 0x02 示例

```Java
public class ReTest {
	static void setStu(Student stu, int x) {
      	System.out.println("改变前stu变量："+stu+"##x值："+x);
		stu = new Student("lisi", 21);
		x=20;
		System.out.println("stu变量："+stu+"##x值："+x);
	}

	public static void main(String[] args) {
		Student p = new Student("jenson", 10);
		int i = 10;
		System.out.println("改变前p变量："+p+"##i值："+i);
		setStu(p, i);
		System.out.println("改变后p变量："+p+"##i值："+i);
	}

	static class Student {
		int age;
		String name;

		Student(String name, int age) {
			this.age = age;
			this.name = name;
		}
	}
}

```

代码很简单，打印结果如下：

```
改变前p变量：thread.ReTest$Student@7852e922##i值：10
改变前stu变量：thread.ReTest$Student@7852e922##x值：10
stu变量：thread.ReTest$Student@4e25154f##x值：20
改变后p变量：thread.ReTest$Student@7852e922##i值：10
```

参考打印结果可以发现：

**基本类型是值传递，而引用类型为Student变量p和参数stu是两个不同的变量但指向同一块内存，在函数setStu中给参数stu从新赋值时，不影响变量p的引用。证明了参数stu是p变量的一个值拷贝。**

