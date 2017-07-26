---
title: 浅谈Android事件传递机制
date: 2017-07-24 17:34:17
tags: [Android, 源码解析]
categories: [Android, 源码解析]
---

# 0x00 前言

为什么点下屏幕某个位置时候，系统会找到正确地控件来响应？

为什么按下按下返回键系统会让当前窗口页面关闭而不是下面的那个页面？

很多时候多问自己个为什么就发现自己会的其实还很少。

# 0x01 冯诺依曼模型回顾

![冯诺依曼模型](http://othg5ggzi.bkt.clouddn.com/%E5%86%AF%E8%AF%BA%E4%BE%9D%E6%9B%BC%E6%A8%A1%E5%9E%8B.png)

讲事件传递为什么把冯诺依曼模型带进来了？

事件传递的事件来自哪里？

基本的事件来自按键、屏幕，属于输入设备，而最终事件的结果要显示在屏幕上(输出设备)，这样才算完成一次交互。其实这里屏幕同时兼顾输入和输出。

有了输入设备，系统是如何从中采集数据的呢？

# 0x02 设备节点到ViewRootImpl

## Android系统是基于Linux的，而输入事件是接收是由Linux内核完成的。

当发现输入设备可用时，Linux内核会在/dev/input/目录下创建名称为event0~n的设备节点。当设备不可用时则删除对应节点。

*如果刚才的不好理解，那么可以理解windows这种情况：当我们新买的鼠标插入USB口时，右下角一般弹窗正在安装USB驱动，而最终会在设备管理中多出一个USBHub设备节点，拔掉USB时就会消失*

输入设备准备就绪，当有事件发生时，Linux内核收到硬件中断，然后把中断加工成原始输入事件存入对应设备节点中。

输入事件已经有了，用户空间只要读取事件信息就可以了。

## 在用户空间这块输入事件的负责人是谁呢？

Android系统为我们指定好了负责人：InputManagerService(后面直接称：IMS)。

IMS和WMS，AMS一样都属于系统服务，在系统启动时由SystemServer统一启动。所以只要看看源码才能知道具体情况如何。

首先看下`SystemServer`启动情况：

```java
    /**
     * The main entry point from zygote.
     */
    public static void main(String[] args) {
        new SystemServer().run();
    }

private void run() {
      // Start services.
        try {
            startBootstrapServices();
            startCoreServices();
            startOtherServices();
        } catch (Throwable ex) {
            Slog.e("System", 	"******************************************");
            Slog.e("System", "************ Failure starting system services", ex);
            throw ex;
        }
}
```

通过源码发现`SystemServer`通过main方法直接启动的。当然这里run方法代码只是关键的一部分，如果都粘贴上来会影响分析。

那么IMS在哪启动的呢？其实IMS通过`startOtherServices`方法启动。

```java
    private void startOtherServices() {
         WindowManagerService wm = null;
        InputManagerService inputManager = null;

		 wm = WindowManagerService.main(context, inputManager,
                    mFactoryTestMode != FactoryTest.FACTORY_TEST_LOW_LEVEL,
                    !mFirstBoot, mOnlyCore);
            ServiceManager.addService(Context.WINDOW_SERVICE, wm);
            ServiceManager.addService(Context.INPUT_SERVICE, inputManager);

            inputManager = new InputManagerService(context);
           inputManager.setWindowManagerCallbacks(wm.getInputMonitor());
            inputManager.start();
	}
```

这里不仅启动了IMS，还有WMS，还给IMS设置了一个`wm.getInputMonitor()`，这个设置有什么用先不用管。最后调用了start方法。

目前只只知道IMS是用户空间的输入事件负责人，具体如何负责还要继续看，IMS的start方法调用了`        nativeStart(mPtr);`，看的出来这是要进入C层实现了。

找到Java层IMS对应的C层实现，打开文件找到`nativeStart`实现：

```c++
static void nativeStart(JNIEnv* env, jclass clazz, jlong ptr) {
    NativeInputManager* im = reinterpret_cast<NativeInputManager*>(ptr);

    status_t result = im->getInputManager()->start();
    if (result) {
        jniThrowRuntimeException(env, "Input manager could not be started.");
    }
}
```

方法如此简单，又是个躯壳，仅仅调用了其他类的方法。

下面看看`start()`里面干了什么，

注意一点：这里显示的是`NativeInputManager`，但其实找到的是`InputManager`,多态特性。

```C++
status_t InputManager::start() {    
    status_t result = mDispatcherThread->run("InputDispatcher", PRIORITY_URGENT_DISPLAY);
    if (result) {
        ALOGE("Could not start InputDispatcher thread due to error %d.", result);
        return result;
    }

    result = mReaderThread->run("InputReader", PRIORITY_URGENT_DISPLAY);
    if (result) {
        ALOGE("Could not start InputReader thread due to error %d.", result);

        mDispatcherThread->requestExit();
        return result;
    }

    return OK;
}
```

这次真的是全部代码，没有精简，看的出来好像start就启动了两个线程。

mDispatcherThread对应`InputDispatcherThread`类，负责事件分发

mReaderThread对应`InputReaderThread`类，负责事件读取

现在我们基本可以猜到，一个线程读取事件，读取后把事件信息传递给分发线程，由分发线程继续处理。

当然这还都只是猜测，实践是检验真理的唯一标准。

## 看看InputReaderThread干了什么

首先看看`InputReaderThread`源码

```C++
/* Reads raw events from the event hub and processes them, endlessly. */
class InputReaderThread : public Thread {
public:
    InputReaderThread(const sp<InputReaderInterface>& reader);
    virtual ~InputReaderThread();

private:
    sp<InputReaderInterface> mReader;

    virtual bool threadLoop();
};
```

`InputReaderThread`继承了Thread，在执行run方法时会回调`threadLoop`。从`virtual`知道这是一个虚函数，来看看具体实现：

```C++
bool InputReaderThread::threadLoop() {
    mReader->loopOnce();
    return true;
}
```

mReader对应`InputReader`类，看下`loopOnce`方法实现：

```C++

void InputReader::loopOnce() {
    int32_t oldGeneration;
    int32_t timeoutMillis;
    bool inputDevicesChanged = false;
    Vector<InputDeviceInfo> inputDevices;
    { // acquire lock
        AutoMutex _l(mLock);

        oldGeneration = mGeneration;
        timeoutMillis = -1;

        uint32_t changes = mConfigurationChangesToRefresh;
        if (changes) {
            mConfigurationChangesToRefresh = 0;
            timeoutMillis = 0;
            refreshConfigurationLocked(changes);
        } else if (mNextTimeout != LLONG_MAX) {
            nsecs_t now = systemTime(SYSTEM_TIME_MONOTONIC);
            timeoutMillis = toMillisecondTimeoutDelay(now, mNextTimeout);
        }
    } // release lock
    //这里就是我们开始说的从设备节点读取输入事件的函数了。
    size_t count = mEventHub->getEvents(timeoutMillis, mEventBuffer, EVENT_BUFFER_SIZE);

    { // acquire lock
        AutoMutex _l(mLock);
        mReaderIsAliveCondition.broadcast();

        if (count) {
          //处理事件
            processEventsLocked(mEventBuffer, count);
        }

        if (mNextTimeout != LLONG_MAX) {
            nsecs_t now = systemTime(SYSTEM_TIME_MONOTONIC);
            if (now >= mNextTimeout) {
#if DEBUG_RAW_EVENTS
                ALOGD("Timeout expired, latency=%0.3fms", (now - mNextTimeout) * 0.000001f);
#endif
                mNextTimeout = LLONG_MAX;
                timeoutExpiredLocked(now);
            }
        }

        if (oldGeneration != mGeneration) {
            inputDevicesChanged = true;
            getInputDevicesLocked(inputDevices);
        }
    } // release lock

    // Send out a message that the describes the changed input devices.
    if (inputDevicesChanged) {
        mPolicy->notifyInputDevicesChanged(inputDevices);
    }

    // Flush queued events out to the listener.
    // This must happen outside of the lock because the listener could potentially call
    // back into the InputReader's methods, such as getScanCodeState, or become blocked
    // on another thread similarly waiting to acquire the InputReader lock thereby
    // resulting in a deadlock.  This situation is actually quite plausible because the
    // listener is actually the input dispatcher, which calls into the window manager,
    // which occasionally calls into the input reader.
    mQueuedListener->flush();
}
```

上面方法主要执行了三个流程：

①通过`mEventHub->getEvents`读取事件

​	这里不再继续深入，重点理解②③

②通过`processEventsLocked`处理事件

这方法里面有`processEventsForDeviceLocked`方法，这是处理一般事件的方法。

```C++
void InputReader::processEventsForDeviceLocked(int32_t deviceId,
        const RawEvent* rawEvents, size_t count) {
    ssize_t deviceIndex = mDevices.indexOfKey(deviceId);
    if (deviceIndex < 0) {
        ALOGW("Discarding event for unknown deviceId %d.", deviceId);
        return;
    }

    InputDevice* device = mDevices.valueAt(deviceIndex);
    if (device->isIgnored()) {
        //ALOGD("Discarding event for ignored deviceId %d.", deviceId);
        return;
    }

    device->process(rawEvents, count);
}
```

这里根据`deviceId`生成`InputDevice`。进而调用`device->process`。

```C++
 for (size_t i = 0; i < numMappers; i++) {
                InputMapper* mapper = mMappers[i];
                mapper->process(rawEvent);
            }
```

最终引出了`InputMapper`，这是一个基类，而对于触摸事件会调用`TouchInputMapper`类的`process`方法：

```C++
void TouchInputMapper::process(const RawEvent* rawEvent) {
    mCursorButtonAccumulator.process(rawEvent);
    mCursorScrollAccumulator.process(rawEvent);
    mTouchButtonAccumulator.process(rawEvent);

    if (rawEvent->type == EV_SYN && rawEvent->code == SYN_REPORT) {
        sync(rawEvent->when);
    }
}
```

后面经过一系列方法调用，事件信息封装到`NotifyMotionArgs args`，通过`getListener()->notifyMotion(&args)`方法把事件加入到队列中。然后开始③过程。



③通过`mQueuedListener->flush()`事件转移到`InputDispatcherThread`。

看看`flush`源码：

```C++
void QueuedInputListener::flush() {
    size_t count = mArgsQueue.size();
    for (size_t i = 0; i < count; i++) {
        NotifyArgs* args = mArgsQueue[i];
        args->notify(mInnerListener);
        delete args;
    }
    mArgsQueue.clear();
}
```

这个方法遍历事件，同时执行`notify`，然后从队列删除消息，

`NotifyArgs`也是基类，有多种子类，其中触摸事件为`NotifyMotionArgs`，其`notify`方法如下：

```C++
void NotifyMotionArgs::notify(const sp<InputListenerInterface>& listener) const {
    listener->notifyMotion(this);
}
```

这里的`listener`指向`InputDispatcher`，而`InputDispatcher`隶属于`InputDispatcherThread` ，下一节继续



## 看看InputDispatcherThread干了什么

`InputDispatcherThread`执行的时候调用了`threadLoop`然后调用了`mDispatcher->dispatchOnce();`。经过一些列调用会转战到`InputChannel::sendMessage`。

继续上一节，看看`InputDispatcher`中`notifyMotion`方法：

```C++
  void InputDispatcher::notifyMotion(const NotifyMotionArgs* args) {

  	needWake = enqueueInboundEventLocked(newEntry);
       if (needWake) {
        mLooper->wake();
    }
  }
```

方法主要做了这两件事：

①事件再次加入队列

​	其中还执行了`findTouchedWindowAtLocked`查找当前活动窗口并拿到句柄。

②唤醒looper。



> 这里疑惑的一点是：事件加入队列，唤醒looper，接下来应该是looper循环读取消息队列进行事件分发，但是这里没有找到和dispatchOnce想关联的地方。可能有疏漏地方。以后发现问题会及时更新上。这里直接继续InputChannel::sendMessage来说。



`InputChannel`是负责与app窗口进行通信的，通过WMS找到对应的窗口。那么`InputChannel`到底是怎么找到窗口的？

在WMS的`addWindow`方法中有这样几行代码：

```c++
public int addWindow(Session session, IWindow client, int seq,
            WindowManager.LayoutParams attrs, int viewVisibility, int displayId,
            Rect outContentInsets, Rect outStableInsets, InputChannel outInputChannel) {
            
                
                String name = win.makeInputChannelName();
                InputChannel[] inputChannels = InputChannel.openInputChannelPair(name);
                win.setInputChannel(inputChannels[0]);
                inputChannels[1].transferTo(outInputChannel);

                mInputManager.registerInputChannel(win.mInputChannel, win.mInputWindowHandle);
            
            }
```

这里是在Activity初始化时想WMS添加的窗口的调用方法，其中在添加窗口时候就已经给这个窗口设置了通信通道。

然后看下`InputChannel.openInputChannelPair`里面做了什么：

```c++
status_t InputChannel::openInputChannelPair(const String8& name,
        sp<InputChannel>& outServerChannel, sp<InputChannel>& outClientChannel) {
    int sockets[2];
    if (socketpair(AF_UNIX, SOCK_SEQPACKET, 0, sockets)) {
        status_t result = -errno;
        ALOGE("channel '%s' ~ Could not create socket pair.  errno=%d",
                name.string(), errno);
        outServerChannel.clear();
        outClientChannel.clear();
        return result;
    }

    int bufferSize = SOCKET_BUFFER_SIZE;
    setsockopt(sockets[0], SOL_SOCKET, SO_SNDBUF, &bufferSize, sizeof(bufferSize));
    setsockopt(sockets[0], SOL_SOCKET, SO_RCVBUF, &bufferSize, sizeof(bufferSize));
    setsockopt(sockets[1], SOL_SOCKET, SO_SNDBUF, &bufferSize, sizeof(bufferSize));
    setsockopt(sockets[1], SOL_SOCKET, SO_RCVBUF, &bufferSize, sizeof(bufferSize));

    String8 serverChannelName = name;
    serverChannelName.append(" (server)");
    outServerChannel = new InputChannel(serverChannelName, sockets[0]);

    String8 clientChannelName = name;
    clientChannelName.append(" (client)");
    outClientChannel = new InputChannel(clientChannelName, sockets[1]);
    return OK;
}
```

这里主要创建了两个`InputChannel`，一个服务端一个客户端，这里的的`InputChannel`是基于socket的，这个socket不是网络的socket而是Unix特有的关于文件的Unix domain socket。

## WMS和ViewRootImpl

经过一些列的调转，最终会回调到`InputEventReceiver`的`dispatchInputEvent`方法。

这个方法是C++通过JNi回调的：

```java
    // Called from native code.
    @SuppressWarnings("unused")
    private void dispatchInputEvent(int seq, InputEvent event) {
        mSeqMap.put(event.getSequenceNumber(), seq);
        onInputEvent(event);
    }
```

上面的`InputEventReceiver` 对象其实是`WindowInputEventReceiver` 的引用，所以`onInputEvent`其实调用的`WindowInputEventReceiver`方法：

```java
		@Override
        public void onInputEvent(InputEvent event) {
            enqueueInputEvent(event, this, 0, true);
        }
```

`enqueueInputEvent`通过连续调用，跳转到`deliverInputEvent`：

```java
    private void deliverInputEvent(QueuedInputEvent q) {
        Trace.asyncTraceBegin(Trace.TRACE_TAG_VIEW, "deliverInputEvent",
                q.mEvent.getSequenceNumber());
        if (mInputEventConsistencyVerifier != null) {
            mInputEventConsistencyVerifier.onInputEvent(q.mEvent, 0);
        }

        InputStage stage;
        if (q.shouldSendToSynthesizer()) {
            stage = mSyntheticInputStage;
        } else {
            stage = q.shouldSkipIme() ? mFirstPostImeInputStage : mFirstInputStage;
        }

        if (stage != null) {
            stage.deliver(q);
        } else {
            finishInputEvent(q);
        }
    }
```

这里的`stage`是多态形式，但是最终会调用`ViewPostImeInputStage`的`onProcess`方法。

```java
       @Override
        protected int onProcess(QueuedInputEvent q) {
            if (q.mEvent instanceof KeyEvent) {
                return processKeyEvent(q);
            } else {
                // If delivering a new non-key event, make sure the window is
                // now allowed to start updating.
                handleDispatchDoneAnimating();
                final int source = q.mEvent.getSource();
                if ((source & InputDevice.SOURCE_CLASS_POINTER) != 0) {
                    return processPointerEvent(q);
                } else if ((source & InputDevice.SOURCE_CLASS_TRACKBALL) != 0) {
                    return processTrackballEvent(q);
                } else {
                    return processGenericMotionEvent(q);
                }
            }
        }
```

由于是触摸事件，所以会执行`processPointerEvent`方法：

```java
       private int processPointerEvent(QueuedInputEvent q) {
            final MotionEvent event = (MotionEvent)q.mEvent;

            mAttachInfo.mUnbufferedDispatchRequested = false;
            boolean handled = mView.dispatchPointerEvent(event);
            if (mAttachInfo.mUnbufferedDispatchRequested && !mUnbufferedInputDispatch) {
                mUnbufferedInputDispatch = true;
                if (mConsumeBatchedInputScheduled) {
                    scheduleConsumeBatchedInputImmediately();
                }
            }
            return handled ? FINISH_HANDLED : FORWARD;
        }
```

发现有` boolean handled = mView.dispatchPointerEvent(event);`这么一句话，看来现在已经开始向view分发事件了。

这里的`mView`是指向`DecorView`，剩下的内容下节继续



# 0x03 ViewRootImpl到View 

通过View的`dispatchPointerEvent`调用了`dispatchTouchEvent`，

因为`DecorView`重写了`dispatchTouchEvent`方法，看下这个内容：

```java
  @Override
        public boolean dispatchTouchEvent(MotionEvent ev) {
            final Callback cb = getCallback();
            return cb != null && !isDestroyed() && mFeatureId < 0 ? cb.dispatchTouchEvent(ev)
                    : super.dispatchTouchEvent(ev);
        }
```

这里的`getCallback`返回的是Activity引用，如果Activity没有销毁，则调用Activity的`dispatchTouchEvent`方法。代码如下：

```java
    public boolean dispatchTouchEvent(MotionEvent ev) {
        if (ev.getAction() == MotionEvent.ACTION_DOWN) {
            onUserInteraction();
        }
        if (getWindow().superDispatchTouchEvent(ev)) {
            return true;
        }
        return onTouchEvent(ev);
    }
```

这里注意一点，如果`getWindow().superDispatchTouchEvent(ev)`返回true，则不继续执行Activity的`onTouchEvent`。

这里的getWinddow就是PhoneWindow，而PhoneWindow的`superDispatchTouchEvent`直接引用了DecorView的`superDispatchTouchEvent`，接着调用了`super.dispatchTouchEvent(event)`，这里的super是ViewGroup，这里主要做了两件事：①判断当前ViewGroup是否拦截事件，如果拦截则执行`onInterceptTouchEvent`。②如果不拦截，事件也没取消，则事件向子View分发。在向子View分发前会先判断子View是否接受事件及事件是否在子View范围内。如果都为true了则调用`dispatchTransformedTouchEvent`。这里面调用了子View的`dispatchTouchEvent`。

子View的`dispatchTouchEvent`里面会判断`touchListener.onTouch`方法和`onTouchEvent`方法。



# 0x04 事件传递须知

 ①如果DecorView的`dispatchPointerEvent`方法返回true表示事件消费成功。

②如果Activity没有销毁，则回调Activity的`dispatchTouchEvent`,如果phone window的`superDispatchTouchEvent`返回true表示被DecorView或其子View消耗了事件，则不执行Activity的onTouchEvent方法。

③如果ViewGroup执行dispatchTouchEvent方法，先判断是否需要拦截事件：

如果需要拦截则执行onInterceptTouchEvent方法。

如果拦截返回true，则不继续事件传递，如果返回false，则继续，首先判断子View是否接受事件及是否在范围内，如果都符合则传递给子View。

④如果子View执行dispatchTouchEvent方法，会先判断是否设置onTouchListener，

如果设置了则执行onTouch方法。如果该方法返回true，则不继续执行子View 的onTouchEvent方法，否则继续执行ouTouchEvent方法。

⑤如果ouTouchEvent方法执行，actionDown时检查长按事件，actionUp时检查onClick事件。







