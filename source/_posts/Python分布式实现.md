---
title: Python分布式实现
date: 2017-08-17 17:04:20
tags: Python
categori: Python
---

# 0x00前言

本文只是python分布式的简单实现

分布式首先要实现多进程，只是这里的多进程是网络级多进程，比较服务器可能一个北京一个海南，

既然是多进程免不了要用到**multiprocessing**模块了。

当然这里主要用到Queue和multiprocessing模块下的**baseManager**

下面看看具体如何实现：

# 0x01 步骤

这里分布式分为服务进程和工作进程2个。

## 首先创建服务进程：

###  建立队列

这里主要是发送任务和接收结果，所以创建2个队列：task_que和result_que

```python
# step1.建立队列           
task_que = Queue()     
result_que = Queue()   
```

这时创建的队列还只能在本地使用，如果成为网络级还要通过manager注册，也就是第二步

### 把队列通过manager注册到网络

```python
class QueueManager(BaseManager):                               
    pass                                                                                                               
                                                               
def task_queue():                                              
    return task_que                                                                                                        
                                                               
def result_queue():                                            
    return result_que                                          
                                                                                                                          
# step2.把队列通过manager注册到网络                                      
QueueManager.register('get_task_que', callable=task_queue)     
QueueManager.register('get_result_que', callable=result_queue) 
```

参数1为typeid，参数2为调用方法。

这里的typeid在任务进程也要使用，

**这里的注册可以认为是给QueueManager添加了typeid为属性方法，后面通过该方法获得经过处理的callable(也就是Queue队列)。就是说通过typeid方法获得的队列是网络队列**

### 创建并启动manager

```python
step3.创建并启动manager                                          
nager = QueueManager(address=('', 8001), authkey=b'jenson') 
nager.start()                                               
```

**注意：authkey需要为bytes，多个进程要保证authkey相同**

### 通过manager获得网络队列

```python
# step4.通过manager获取网络中的Queue       
task = manager.get_task_que()      
result = manager.get_result_que()  
```

### 操作网络队列进行任务分发和结果获取

```python
# step5.操作该网络Queue                                     
for url in ["ImageUrl_" + str(i) for i in range(10)]:  
    print('put task %s ...' % url)                     
    task.put(url)                                      
    time.sleep(1)                                      
                                                       
print('try get result...')                             
for i in range(10):                                    
    print('result is %s' % result.get(timeout=10))     
    time.sleep(1)                                      
# 关闭管理                                                 
manager.shutdown()                                     
                                                       
```

## 接下来创建任务进程

### 通过manager注册

```python
class QueueManager(BaseManager):         
    pass                                 
 # step1.注册和服务进程                         
QueueManager.register('get_task_que')    
QueueManager.register('get_result_que')  
```

### 创建并连接manager

```python
# step2.创建并启动manager
m = QueueManager(address=('127.0.0.1', 8001), authkey=b'jenson')
m.connect()
```

### 获取网络队列

```python
# step3.通过manager获取网络队列
task = m.get_task_que()  # type: Queue
result = m.get_result_que()  # type: Queue
```

### 操作网络队列

```python
while (not task.empty()):
    url = task.get(True, 5)
    print('download==' + url)
    time.sleep(1)
    result.put('already download' + url)
```

# 执行结果

服务进程首先执行，结果如下：

```
put task ImageUrl_0 ...
put task ImageUrl_1 ...
put task ImageUrl_2 ...
put task ImageUrl_3 ...
put task ImageUrl_4 ...
put task ImageUrl_5 ...
put task ImageUrl_6 ...
put task ImageUrl_7 ...
put task ImageUrl_8 ...
put task ImageUrl_9 ...
try get result...

```

然后运行工作进程，结果如下：

```
download==ImageUrl_0
download==ImageUrl_1
download==ImageUrl_2
download==ImageUrl_3
download==ImageUrl_4
download==ImageUrl_5
download==ImageUrl_6
download==ImageUrl_7
download==ImageUrl_8
download==ImageUrl_9
```

最后服务进程又收到信息如下：

```
result is already downloadImageUrl_0
result is already downloadImageUrl_1
result is already downloadImageUrl_2
result is already downloadImageUrl_3
result is already downloadImageUrl_4
result is already downloadImageUrl_5
result is already downloadImageUrl_6
result is already downloadImageUrl_7
result is already downloadImageUrl_8
result is already downloadImageUrl_9
```

