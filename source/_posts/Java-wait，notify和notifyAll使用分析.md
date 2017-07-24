---
title: Java wait，notify和notifyAll使用分析
date: 2017-07-21 13:00:51
tags: [Java, 多线程]
categories: [Java, 多线程]
---

# 0x00 前言

提起多线程想当然的就会想到`synchronized` 。但是`synchronized`只能用于线程同步，如果是线程交替执行(生产者/消费者)，`synchronized`也无能为力了。这时wait，notify和notifyAll派上用场了。

# 0x01 它们只是Object里的方法

点开源码发现这三个方法只是object里面的方法，和多线程有神马关系呢？我相信不止我一个人曾经有过这个疑问。

不只是object里的方法，而且还都是native方法。这是Java有意为之。因为Java设计之初就是要每个对象都有一个监控锁(Monitor)。而wait，notify和notifyAll三个方法也必须要在当前线程已经持有监控锁状态下才能执行，否则就会报`java.lang.IllegalMonitorStateException`。

# 0x02 怎么知道线程是否持有状态锁

线程同步原理就是利用了锁的互斥，所以在`synchronized`代码块内能确定是持有锁的。也就是说wait，notify和notifyAll方法需要在`synchronized`代码块内使用。

具体来说有三种方式：

- 同步对象方法持有锁

  ```java
  	public synchronized void sync_1() {
  	} 
  ```

- 同步静态方法持有锁

  ```java
  	public static synchronized void sync_2() {
  	}
  ```

  ​

- 同步代码块持有锁

  ```java
  	public  void sync_3() {
  		synchronized(lock){
  		}
  	}
  ```

# 0x03 三个方法都有什么用

- wait后会让当前线程处于阻塞状态

  能调用wait是因为当前线程持有Monitor，但是调用wait后，当前线程会释放锁，给予其他线程执行的机会。

- notify会唤醒一个因为该对象阻塞的线程

  同样notify能正常调用是因为当前线程持有锁，当notify执行后会唤醒其他阻塞线程，但是那些阻塞线程并不会立即执行，因为Monitor被notify线程持有。被唤醒的线程要等`synchronized`代码块执行完毕释放Monitor后才能执行。

- notifyAll会唤醒所有因为该对象阻塞的线程

  同notify。

# 0x04 使用示例

```java
	public static void main(String[] args) {
		Thread t1 = new Thread(new Runnable() {
			@Override
			public void run() {
				System.out.println("start-t1");
				synchronized (lock) {
					try {
						System.out.println("t1等待唤醒");
						lock.wait();
					} catch (Exception e) {
						e.printStackTrace();
					}
					System.out.println("t1被唤醒持有锁");
				}
			}
		});

		Thread t2 = new Thread(new Runnable() {
			@Override
			public void run() {
				System.out.println("start-t2");
				try {
					Thread.currentThread().sleep(3000);
				} catch (InterruptedException e1) {
					e1.printStackTrace();
				}
				synchronized (lock) {
					lock.notify();// 唤醒
					System.out.println("t2持有锁，唤醒t1");
					try {
						Thread.currentThread().sleep(3000);
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
					System.out.println("t2休眠3s");
				}
			}
		});
		t1.start();
		t2.start();
	}
```

执行结果如下：

start-t2
start-t1
t1等待唤醒
t2持有锁，唤醒t1
t2休眠3s
t1被唤醒持有锁

在t2中调用notify唤醒t1后，t2又休眠了3s钟，期间t1仍然没有执行，可见确实如上所说，唤醒的线程要在notify线程释放Monitor后才能执行。


