---
title: 使用sychronized注意事项
date: 2017-08-10 21:43:15
tags: Java
categories: Java
---

# 0x00 前言

Java编程中多线程并发是一个举足轻重的模块，所以加深对这一块的理解是很有必要的。

# 0x01锁定对象而非函数或代码块 

- 当sychronized修饰实例方法时，锁定的是调用该方法的对象

  如下代码：

  ```java
  class Text{
    public sychronized void method1(){
    }
  }
  ```

- 当sychronized锁定this时，锁定的也是当前对象

  ```java
  class Text{
    public void method2(){
      sychronized(this){ 
      }
    }
  }
  ```

  **既然锁定的是对象，也就意味着调用该函数的线程A会获得该对象lock，同样的，线程B在调用该对象的函数时也要获得该对象的lock，但是该对象的lock此时被线程A持有，所以线程B就要处于阻塞状态，直到lock被线程A释放。**

  看看下面代码有何问题：

  ```java
  static class TThread extends Thread {
  		private int key;

  		public TThread(int key) {
  			this.key = key;
  		}

  		@Override
  		public void run() {
  			go(this.key);
  		}

  		public synchronized void go(int key) {
  			while (true) {
  				System.out.println(key);
  				try {
  					Thread.currentThread().sleep(1000);
  				} catch (InterruptedException e) {
  					e.printStackTrace();
  				}
  			}
  		}
  	}

  	static class SecondThread extends Thread {
  		TThread tt;

  		public SecondThread(TThread tt) {
  			this.tt = tt;
  		}

  		@Override
  		public void run() {
  			this.tt.go(3);
  		}
  	}

  	public static void main(String[] args) {
  		TThread t1 = new TThread(1);
  		t1.start();
  		SecondThread st = new SecondThread(t1);
  		st.start();
  		TThread t2 = new TThread(2);
  		t2.start();
  	}
  ```

  上述代码会打印1和2，但是却不会打印3。因为synchronized锁定的是线程对象，而t1和t2是两个不同的对象，所以可以并发执行。而实例st中run方法实际执行的是t1的同步方法，因而1和3不会同时打印。

  **另一方面，既然是锁定对象，意味着每个对象都有自己的lock，如果是同一个类的不同实例在多个线程间调用时，仍然会并发执行。**

# 0x02 锁定类Class对象

- 当sychronized修饰static方法时，锁定的是该class的Class对象

  ```java
  class Text{
    public sychronized static void method1(){
    }
  }
  ```

- 当sychronized锁定xx.class时，锁定的也是该class的Class对象

  ```java
  class Text{
    public void method2(){
      sychronized(Text.class){
      }
    }
  }
  ```

  如下代码有何问题：

  ```java
  	static class TThread extends Thread {

  		@Override
  		public void run() {
  			go1();
  		}

  		private synchronized void go1() {
  			while (true) {
  				System.out.println(1);
  				try {
  					Thread.currentThread().sleep(1000);
  				} catch (InterruptedException e) {
  					e.printStackTrace();
  				}
  			}
  		}

  		public synchronized static void go2() {
  			while (true) {
  				System.out.println(2);
  				try {
  					Thread.currentThread().sleep(1000);
  				} catch (InterruptedException e) {
  					e.printStackTrace();
  				}
  			}
  		}
  	}

  	public static void main(String[] args) {
  		TThread t1 = new TThread();
  		t1.start();
  		t1.go2();
  	}
  ```

  go1函数是实例函数，go2是类函数。尽管上述两个函数都声明为synchronized，它们并非线程安全的。因为两个线程获取的是不同的lock。

  因此上述代码执行时，打印1和2交替执行。

  **时刻注意对象锁和类锁的区分，不要为了实现某些数据同步而同时使用两种方法。因为这两种方法获取的是不同的锁**

# 0x03 避免因权限导致锁无效

直接看如下代码有何问题：

```java
	public int[] intArr = new int[10];

	public synchronized void addArr(int[] arr) {
		int len = intArr.length;
		if (len == arr.length) {
			for (int i = 0; i < len; i++) {
				intArr[i] += arr[i];
			}
		}
	}

	public synchronized void subtrackArr(int[] arr) {
		int len = intArr.length;
		if (len == arr.length) {
			for (int i = 0; i < len; i++) {
				intArr[i] -= arr[i];
			}
		}
	}
```

上面代码是线程安全的吗？

当然不是。虽然两个实例方法实现了线程同步，但是还有其他漏洞。

首先要明白同步的目的是保证**intArr**的准确性。但是因为该实例属性权限是**public**状态。这导致无须通过同步方法，也能修改数组。所以应该修改权限为私有状态。

**有时看似添加了同步方法就以为万无一失了，但是就因为权限开放导致线程安全没有生效**

# 0x04 避免无谓的同步导致性能降低

看看如下代码正确吗：

```Java
	private int[] arr1;
	private int[] arr2;
	private int[] arr3;
	private int[] arr4;

	public synchronized void method1() {
		// 操作arr1和arr2
	}

	public synchronized void method2() {
		// 操作arr1和arr2
	}

	public synchronized void method3() {
		// 操作arr3和arr4
	}

	public synchronized void method4() {
		// 操作arr3和arr4
	}
```

上述操作无疑是线程安全的，但是这种方法同步带来了很大的性能问题。

因为同步所以互斥，这也就决定了四个方法不能并发，但是逻辑上，method1和method2由于都操作了arr1和arr2，所以应该互斥，但是不应该和method3、method4互斥。

但是现在的结果是操作arr1和arr2的时候就不能操作arr3和arr4。造成了性能下降。

要避免这种情况可以缩小锁范围，**创建专门的对象锁以替代方法所属对象的锁**

如下修改：

```java
	private int[] arr1;
	private int[] arr2;
	private int[] arr3;
	private int[] arr4;
	private byte[] lock1 = new byte[0];
	private byte[] lock2 = new byte[0];

	public void method1() {
		// 操作arr1和arr2
		synchronized (lock1) {
		}
	}

	public void method2() {
		// 操作arr1和arr2
		synchronized (lock1) {
		}
	}

	public void method3() {
		// 操作arr3和arr4
		synchronized (lock2) {
		}
	}

	public void method4() {
		// 操作arr3和arr4
		synchronized (lock2) {
		}
	}
```

# 0x05 避免多个锁顺序不同导致死锁

要避免死锁首先要知道什么是死锁，以及死锁如何发生得。

**线程A申请一个对象锁，但是对象锁被线程B持有了，而线程B申请的对象锁又被线程A持有了，这样导致两个线程分别阻塞了对方，这就是死锁。**

死锁是如何形成的？

**死锁一般是由于多个锁对象在不同的线程中获取对象锁顺序不同造成的**

看如下代码：

```java
	static class TT {
		public void method1(int[] arr1, int[] arr2) {
			// 操作arr1和arr2
			synchronized (arr1) {
				synchronized (arr2) {

				}
			}
		}
	}

	public static void main(String[] args) {
		TT tt = new TT();
		int[] arr1 = new int[10];
		int[] arr2 = new int[10];
		tt.method1(arr1, arr2);
		tt.method1(arr2, arr1);

	}
```

在method1的方法调用中，因为两个数组参数的顺序调换，导致了多线程情况下获取对象锁顺序不同，导致死锁发生。

如何解决死锁？

**一种简单的方法就是保证不同线程中多个锁对象获取锁的顺序是相同的。**



