---
title: 设计一个有获取元素最小值getMin的栈
date: 2017-05-17 16:24:42
tags: 算法
categories: 算法
---

# 0x00 前言

栈可以看成一个瓶子，只有一个口，另一端被封底。这样数据进出都只能从一个口经过。这样就导致的一个直接结果就是数据FILO问题。要找到栈中最小数据，单靠一个栈不能完成，需要外部提供辅助。

# 0x01 分析



一个存好数据的栈，要从里面找到最小数值，不可避免的要把数据出栈，然后进行对比。这时有个问题就来了，找到最小数值后，其他数据还有用吗？换句话说，其他数据还要不要了？如果不要的话，比较完成后不用再次存储，如果要德话，比较期间还要注意数据的保存。这里注意要分这两种情况来考虑。

<!-- more -->

# 0x02 不要其他数据的实现

既然不需要其他数据，那就直接把数据弹出栈就好了，每次弹栈时对比临时值，如果当前弹栈数据比临时值大则直接弹栈，否则就把值赋给临时值。

```
public class StackMin {
	private Stack<Integer> stack = new Stack<>();

	public int getStackMin() {
		int min = 0;
		while (!stack.isEmpty()) {
			int current = stack.pop();
			if (min != 0) {
				if (min > current) {
					min = current;
				}
			} else {
				min = current;
			}
		}
		return min;
	}

	public static void main(String[] args) {
		StackMin stackMin = new StackMin();
		stackMin.stack.push(9);
		stackMin.stack.push(5);
		stackMin.stack.push(2);
		stackMin.stack.push(7);
		System.out.println(stackMin.getStackMin());
	}
}

```

这里代码只实现了getStackMin方法，栈的其他方法就直接定义一个方法后直接调用Stack方法即可。

# 0x03 需要其他数据的实现

如果要保留栈的数据，那最直接的想法是在弹栈比较期间，把弹出栈的数据顺路给入道一个新的栈中，这里相比上面，需要多一个栈即可实现。

```
public class StackMin {
	private Stack<Integer> stack = new Stack<>();
	private Stack<Integer> backUp = new Stack<>();//备份栈

	public int getStackMin() {
		int min = 0;
		while (!stack.isEmpty()) {
			int current = stack.pop();
			if (min != 0) {
				if (min > current) {
					min = current;
				}
			} else {
				min = current;
			}
			backUp.push(current);//弹出栈时进入备份栈
		}
		return min;
	}

	public static void main(String[] args) {
		StackMin stackMin = new StackMin();
		stackMin.stack.push(9);
		stackMin.stack.push(5);
		stackMin.stack.push(2);
		stackMin.stack.push(7);
		System.out.println(stackMin.getStackMin());
	}
}

```

