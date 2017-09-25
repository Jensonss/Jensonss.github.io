---
title: Python入门系列(5)：进程、线程和协程
date: 2017-08-17 15:22:54
tags: Python
categories: Python
---

# 前言

如今硬件发展速度很快，就连手机cpu都是四核八核起步了。那么如何充分发掘、利用强大的cpu资源是作为研发人员的我们需要考虑的事情了。

# 并发和并行

**知己知彼，百战不殆**，要充分利用cpu首先要了解cpu是如何调用程序的。

在线程出现之前，cpu的调度单位是进程，每个程序启动就生成一个独立的进程。

进程拥有独立的内存空间，进行IPC难度较大，且cpu对进程上下文切换耗时较多，种种因素催生了进程的亲戚~线程，自此cpu的最小调度单位就是线程了。

进程，又叫重量级进程，而线程，又叫轻量级进程，之所以是轻量级，是因为多个线程共享进程空间，使得多任务执行时，多线程进行数据交换容易了不少，而且上下文切换也更容易。

对进程和线程有了了解，那多并发和并行又直到多少呢？并行和并发有什么区别？

**并行，同时运行，可以理解为多个不同实体处理多个任务**，

**并发，同时发生，可以理解为一个实体不同时间段处理多个任务**，

**所以并行可以理解为硬件上的，并发理解为逻辑上的**。

<!-- more -->

对以上都有了了解，下面看看在Python中如何使用：

# 进程

## 创建进程

- os.fork()

  fork⽅法来⾃于Unix/Linux操作系统中提供的⼀个fork系统调⽤， 这个⽅法⾮常特殊。 

  普通的⽅法都是调⽤⼀次， 返回⼀次， ⽽fork⽅法是调⽤⼀次， 返回两次， 

  原因在于操作系统将当前进程（⽗进程） 复制出⼀份进程（⼦进程） ， 这两个进程⼏乎完
  全相同， 于是fork⽅法分别在⽗进程和⼦进程中返回。 ⼦进程中永远返回0， ⽗进程中返回的是⼦进程的ID。

  ```python
  def getPidTest():
      print("当前进程ID：" + str(os.getpid()))

  print("当前进程ID：" + str(os.getpid()))
  pid = os.fork()
  if pid == 0:#子进程
      getPidTest()
  elif pid > 0:# 父进程
      getPidTest()
  ```

  ​

- subprocess

  subprocess模块可以用来创建子进程，但是主要功能是执行外部的命令和程序，而不是运行python文件中的函数，另外这里的IPC只能通过管道进行文本交流。

  比如执行终端命令时：

  ```python
  subprocess.Popen('ls -al', shell=True)
  ```

  ​

- multiprocessing

  multiprocessing模块提供了⼀个Process类来描述⼀个进程对象。

  创建⼦进程时， 只需要传⼊⼀个执⾏函数和函数的参数， 即可完成⼀个Process实例的创建， ⽤start（） ⽅法启动进程， ⽤join（） ⽅法实现进程间的同步。  

  ```python
  def getPidTest():
      print("当前进程ID：" + str(os.getpid()))

  print("当前进程ID：" + str(os.getpid()))
  if __name__ == '__main__':
      p = Process(target=getPidTest, name='p1', args=())
      p.start()
  ```

  **target**表示进程要调用的函数名

  **name**表示进程名

  **args**表示函数参数的元组形式

  **kargs**表示函数参数的字典形式

## IPC

Python提供了多种进程间通信方式如Queue、Pipe。

这里两种方式都体验一下：

- Queue

  Queue是多进程安全的队列， 可以使⽤Queue实现多进程之间的数据传
  递。 有两个⽅法： Put和Get可以进⾏Queue操作：

  **·Put**⽅法⽤以插⼊数据到队列中， 它还有两个可选参数： blocked和timeout。 如果blocked为True（ 默认值） ， 并且timeout为正值， 该⽅法会阻塞timeout指定的时间， 直到该队列有剩余的空间。 如果超时， 会抛出Queue.Full异常。 如果blocked为False， 但该Queue已满， 会⽴即抛出Queue.Full异常。

  **.Get**⽅法可以从队列读取并且删除⼀个元素。 同样， Get⽅法有两个可选参数： blocked和timeout。 如果blocked为True（ 默认值） ， 并且timeout为正值， 那么在等待时间内没有取到任何元素， 会抛出Queue.Empty异常。 如果blocked为False， 分两种情况： 如果Queue有⼀个值可⽤， 则⽴即返回该值； 否则，如果队列为空， 则⽴即抛出Queue.Empty异常。

  ```python
  from multiprocessing import Process, Queue
  import time
  ```


  def write_proc(queue, urls):
      '''
      :type queue Queue
      :param queue:
      :param urls:
      :return:
      '''
      for url in urls:
          print("put:" + url)
          queue.put(url)
          time.sleep(1)#这里睡眠时间是秒，Java中是毫秒


  def read_proc(queue):
      '''
      :type queue Queue
      :param queue:
      :return:
      '''
      while True:
         url = queue.get(True)
         print('get:' + url)


  q =  Queue()
  write1 = Process(target=write_proc,args=(q,['url1','url2','url3']))
  write2 = Process(target=write_proc,args=(q,['url4','url5','url6']))
  read  = Process(target=read_proc,args=(q,))
  write1.start()
  write2.start()
  read.start()
  write1.join()
  write2.join()
  read.terminate()

  ```

  执行结果如下：

  ```
  put:url1
  put:url4
  get:url1
  get:url4
  put:url5
  put:url2
  get:url2
  get:url5
  put:url6
  put:url3
  get:url6
  get:url3
  ```

- Pipe

  Pipe常⽤来在两个进程间进⾏通信， 两个进程分别位于管道的两端。

  Pipe⽅法返回（ conn1， conn2） 代表⼀个管道的两个端。

   Pipe⽅法有duplex参数， 如果duplex参数为True（ 默认值） ， 那么这个管道是全双⼯模式， 也就是说conn1和conn2均可收发。 

  若duplex为False， conn1只负责接收消息， conn2只负责发送消息。 send和recv⽅法分别是发送和接收消息的⽅法。 例如， 在全双⼯模式下， 可以调⽤conn1.send发送消息，conn1.recv接收消息。 如果没有消息可接收， recv⽅法会⼀直阻塞。 如果管道已经被关闭， 那么recv⽅法会抛出EOFError。

  ```python
  from multiprocessing import Process,Pipe
  import time


  def send_proc(pipe,urls):
      '''
      :type pipe Connection
      :param pipe:
      :param urls:
      :return:
      '''
      for url in urls:
          print('send:'+url)
          pipe.send(url)
          time.sleep(1)

  def recv_proc(pipe):
      '''
      :type pipe Connection
      :param pipe:
      :return:
      '''
      while True:
          url = pipe.recv()
          print('recv:'+url)
          time.sleep(1)

  pipe = Pipe()
  p1 = Process(target=send_proc,args=(pipe[0],['url1','url2','url3']))
  p2 = Process(target=recv_proc,args=(pipe[1],))
  p1.start()
  p2.start()
  p1.join()
  p2.join()

  ```

  执行结果如下：

  ```
  send:url1
  recv:url1
  send:url2
  recv:url2
  send:url3
  recv:url3
  ```

  ​

# 线程

## 创建线程

Python中有多个模块都可以实现多线程，比如thread，threading，但是由于thread模块自身一些原因，推荐使用threading模块。

- thread模块

   thread 模块提供了基本的线程和锁定支持。

  ```python
  import _thread
  _thread.start_new_thread(function=getPidTest, args=(),kwargs={})
  ```

  thread 模块的核心函数是 start_new_thread()。它的参数包括函数、函数的参数以及可选的关键字参数。

  **该模块基本已经作为保留模块，原本的thread模块已经变为_thread**  

- threading 模块

  threading模块下有个Thread类，可以帮助我们实现多线程：

  ```python
  import threading

  threading.Thread(target=wrapper,args=(),kwargs={},name="thread1").start()

  ```

  **target**表示线程要调用的函数名

  **name**表示线程名

  **args**表示函数参数的元组形式

  **kargs**表示函数参数的字典形式

## 线程间通信



## 线程同步和锁



# 协程

协程是什么东西呢？

我们知道线程是轻量级进程，而协程又是轻量级线程。

协程拥有⾃⼰的寄存器上下⽂和栈。 协程调度切换时， 将寄存器上下⽂和栈保存到其他地⽅， 在切回来的时候， 恢复先前保存的寄存器上下⽂和栈。 因此协程能保留上⼀次调⽤时的状态， 每次过程重⼊时， 就相当于进⼊上⼀次调⽤的状态。

在并发编程中， 协程与线程类似， 每个协程表⽰⼀个执⾏单元， 有⾃⼰的本地数据， 与其他协程共享全局数据和其他资源。

Python通过yield提供了对协程的基本⽀持， 但是不完全， ⽽使⽤第三⽅gevent库是更好的选择， gevent提供了⽐较完善的协程⽀持。 gevent是⼀个基于协程的Python⽹络函数库， 使⽤greenlet在libev事件循环顶部提供了⼀个有⾼级别并发性的API。



## 创建协程

这里使用gevent库创建协程:

```python
from gevent import monkey;
monkey.patch_all()
import gevent
from  urllib import request

def run_task(url):
    print("visit=="+url)
    try:
        response = request.urlopen(url)
        data = response.read()
        print('%d bytes received from %s.' % (len(data), url))
    except Exception as e:
        print(e)
    # print(threading.Thread.getName())


if __name__ == '__main__':
    urls = ['http://jensondev.me/', 'http://www.baidu.com', 'http://www.cnblogs.com/']
    greenlets = [gevent.spawn(run_task, url) for url in urls]
    gevent.joinall(greenlets)
```

以上程序主要⽤了gevent中的spawn⽅法和joinall⽅法。 spawn⽅法可以看做是⽤来形成协程， joinall⽅法就是添加这些协程任务， 并且启动运⾏。 从运⾏结果来看， 3个⽹络操作是并发执⾏的， ⽽且结束顺序不同， 但其实只有⼀个线程。



gevent中还提供了对池的⽀持。 当拥有动态数量的greenlet需要进⾏并发管理（ 限制并发数） 时， 就可以使⽤池， 这在处理⼤量的⽹络和IO操作时是⾮常需要的：

```python
from gevent import monkey,pool;
monkey.patch_all()
import gevent
from  urllib import request

def run_task(url):
    print("visit=="+url)
    try:
        response = request.urlopen(url)
        data = response.read()
        print('%d bytes received from %s.' % (len(data), url))
    except Exception as e:
        print(e)
    # print(threading.Thread.getName())


if __name__ == '__main__':
    urls = ['http://jensondev.me/', 'http://www.baidu.com', 'http://www.cnblogs.com/']
    # greenlets = [gevent.spawn(run_task, url) for url in urls]
    # gevent.joinall(greenlets)
    p =pool.Pool(2)
    p.map(func=run_task,iterable=urls)
```

