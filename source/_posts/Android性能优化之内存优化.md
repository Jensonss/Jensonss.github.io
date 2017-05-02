---
title: Android性能优化之内存优化
date: 2017-04-21 20:55:54
tags: Android
categories: Android
---
>上一章讲了[Android性能优化之耗电优化
](http://www.jianshu.com/p/bd3d673aa979)，感兴趣的可以看下。这一章来说说Android内存方面如何优化，虽说是讲内存优化但是并不涉及虚拟机底层原理，力求通俗易懂。


![屏幕快照 2017-03-29 下午2.58.41.png](http://upload-images.jianshu.io/upload_images/1796052-0956f76fd0a3e36b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

养成好习惯先上图。内存从状态上来说只有已使用和未使用两种。本章内存优化也从这两方面下手：已使用的内存如何保证虚拟机的顺利回收、未使用的内存如何在满足需求的情况下尽量小的申请。

#### 如何保证已使用内存顺利被回收？
- Java对象生命周期
  - 创建阶段
申请内存空间，构造对象并初始化相关属性值
  - 使用阶段
根据对象应用调用相关方法完成业务逻辑。对象至少被一个强引用持有，除非对象创建时显示声明使用软引用、弱引用和虚引用。
  - 不可见阶段
当一个对象处于不可见阶段时，说明程序本身不再持有该对象的任何强引用，当然对象还是存在着的。
  - 不可达阶段
对象处于不可达阶段是指该对象不再被任何强引用所持有。GC会发现对象已不可达

  - 收集阶段
当垃圾回收器发现该对象已经处于“不可达阶段”并且垃圾回收器已经对该对象的内存空间重新分配做好准备时，则对象进入了“收集阶段”。
  - 终结阶段
当对象执行完finalize()方法后仍然处于不可达状态时，则该对象进入终结阶段。在该阶段，等待垃圾回收器对该对象空间进行回收。

  - 对象空间重新分配阶段
若垃圾回收器对该对象的所占用的内存空间进行回收或者再分配，则该对象彻底消失，这个阶段称之为“对象空间重新分配阶段”。

	以上是Java对象生命周期的简要介绍，要保证内存顺利回收，正确使用Java对象生命周期很重要，如果不能及时回收，我们就称之为“发生了内存泄露”。

>在不可见阶段，程序本身不再持有对象强引用，但对象仍可能被JVM等系统下的某些已装载的静态变量或线程或JNI等强引用持有着，这些特殊的强引用被称为”GC root”。存在着这些GC root会导致对象的内存泄露情况，无法被回收。


![垃圾回收.jpeg](http://upload-images.jianshu.io/upload_images/1796052-ef7e7496b8a9cd2e.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

图中灰色的孤立无援的对象对于GC Roots来说不可达，会被回收。知道了内存泄露会影响回收，下面说下哪些方面会导致内存泄露

- 引起内存泄露的情况
   - 资源没有适时关闭
sqlite的cursor、读写文件使用的File文件流等在使用完后没有及时关闭。虽然cursor会在系统回收时自动关闭，但是这样效率较低。对于资源对象使用还是应该养成良好习惯，使用完毕close并置空。
   - 注册对象未注销
在Android中主要是指注册的广播在Activity销毁时反注销。
在Activity中如果有使用的观察者模式在生命周期发生变化时根据需求注销。
在Activity中使用的各类传感器(光线、重力等)在页面销毁时及时注销，否则不光导致内存泄露还会因为传感器频繁的采样导致耗电及cpu的占用。
   - 使用static修饰变量
这里只说一点，被static修饰的变量可以认为是直接被GC Roots引用了，那你就知道其生命周期有多长了。这时候你如果用static 修饰Bitmap、View、Context和Activity等后果有多严重了吧。

   - 非静态内部类的静态实例

		先看几行代码：

		```
		public class MainActivity extends AppCompatActivity {
		public static People people;
   		 @Override
    		protected void onCreate(Bundle savedInstanceState) {
       		 super.onCreate(savedInstanceState);
       		 setContentView(R.layout.activity_main);
       		 people = new People();
    		}
   		 class  People{
       		 int age ;
       		 String name ;
    		}
		}
		```

		非静态内部类People持有外部类即当前Activity的引用，而该非静态内部类实例又是static修饰		的，导致Activity一直被持有而不得释放，最终导致Activity所包含的view不能释放，如果view 		tree中包含多图片，那泄露的内存是很大的。

   - Handler

		众所周知handler用来发送和处理消息回调的。
handler导致泄露主要是handler实例是作为非静态匿名内部类方式创建，并且MessageQueue队列中有未处理消息，这时如果退出Activity，MessageQueue中还有Message，而Message持有handler实例，handler实例作为非静态内部类持有Activity引用，最终的连锁反应导致Activity泄露。

		>handler引起的内存泄露一般是临时性的，因为消息队列里的Message在延时到时间或者某一情况激活后还是会执行的，除非你是故意搞事情。创建handler时最好使用静态内部类，同时在Activity退出时执行 handler.removeCallbacksAndMessages(null);清空队列消息

   - Webview

		webview的使用总是会莫名的出现各种问题或泄露。最好的办法就是把web页面放在一个独立的进程，如果需要交互使用aidl。

  - 容器中的对象未清理对象

		Android中使用的容器最多的就是List和Map。用来存储对象集合，如果对象集合和页面相关，那么在退出页面时注意清空集合。同时不要使用static修饰集合。

#### 如何尽量小的申请内存？
上面说完了如何保证GC顺利回收，现在来讲讲要最小使用内存应该怎么做：

- 慎用自动封装

	来几行代码尝尝：

	```
        Integer num=0;
        for (int i=0;i<100;i++) {
            num+=i;
        }
	```

	Java基本数据类型是有自动装箱机制的。每次执行循环都会发生一次装箱操作创建一个Integer对象，造成内存消耗。包括其他基本数据类型都有可能造成这种情况。

- 内存复用
  - 视图复用

		在ListView中使用ViewHolder复用item组件，一方面节省内存，一方面提高滑动流畅性。都用过不多介绍。
  - 使用对象池

		看过Handler、Looper、Message、MessageQueue这一套消息循环源码的同志应该知道里面的Message使用了对象池模式。
>对象池类似线程池， 首先初始化一个固定大小池子，每次创建对象时候先去池子中找有没有，如果有直接取出，如果没有new出来使用后还到池子里。这样便可达到对象复用的目的。
对象池模式适用于那些频繁使用创建的对象，比如一个聊天app，里面对象最多的恐怕就是聊天信息(每条聊天信息对应一个信息对象)。都知道对象的创建是很耗费时间和内存的，没事不要new着玩。如果每条消息都创建一个对象，那可想而知该APP的性能。

		对象池的使用也很简单，少量代码即可完成：

		```
			public class People {
        			private static final Pools.SynchronizedPool<People> sPool = new Pools.SynchronizedPool<People>(
           		   	  20);//需要维持对象的数量
        			int age;
       			String name;
      
        			public static People obtain() {
          			  People instance = sPool.acquire();
          			  return (instance != null) ? instance : new People();
        			}
        			public void recycle() {
          			  sPool.release(this);
       		 	}
    			}

		```

		>注意：对象申请(obtain)和释放(recycle)成对出现，使用一个对象后一定要释放还给对象池。



	- Bitmap复用

		如果设置了options.inBitmap属性，以后再使用带有该options参数的decode方法加载图片资源时		，decode会尝试重用已存在的位图内存，这样节省了加载和分配的时间，同时也节省了内存空间

		>该属性从3.0开始引进，低版本不支持inBitmap，4.4系统之前只能重用大小相同的内存区域，4.4以后可以重用任何比所需内存小的区域。具体使用可参考[官网](https://developer.android.com/topic/performance/graphics/manage-memory.html)。

  - 纯色规则形状背景用Color Res代替图片

		经常遇到一些按钮背景是纯色显示，比如选中状态背景变为纯灰，但是设计已经发来了切图用还是不用？大声say NO！如果背景使用图片来显示，那背景每个像素都要绘制。

    	假设一个分辨率为100x100的图片，占用4通道。那该图片内存占用就是100x100x4 =4万Byte≈40KB；但是如果使用```        android:background="@color/colorAccent"```引用color值的方式，由于是纯色，只需渲染一个像素而其他像素复用这个像素值即可。这样只需要4Byte即完成了背景设置。



- 选择合适数据类型

   - 使用ArrayMap替换HashMap

		先看一下HashMap模型和ArrayMap模型：
![hashmap模型.jpg](http://upload-images.jianshu.io/upload_images/1796052-3e3049cb811341c1.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![arraymap模型.jpg](http://upload-images.jianshu.io/upload_images/1796052-0d979501c5d1358f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


>HashMap是一个散列链表，稀疏阵列导致内存稍大，而ArrayMap提供了和HashMap一样的功能，但是避免了内存过度开销。在执行插入或删除操作时，从性能上看ArrayMap比HashMap稍差，但是如果对象数很小，比如1000以内不用担心性能问题。如果想深入了解这2个的原理请自行搜索，这里不过多阐述。


  - 枚举替身来了
      JDK1.5就支持了枚举类型，使用Enum关键字定义。使用枚举类型很多时候出于参数类型安全迫不得已作出的选择。

   ```
  public String  getValue(int type){
            switch (type) {
                case 1:
                    break;
                case 2:
                    break;
                case 3:
                    break;
                default:
                    throw new IllegalArgumentException("不合法参数");
            }
    return "";
    }
```

      试想一下如果一个函数的参数为int type，函数处理时只用到了1，2，3三种值，如果是其他值就抛出异常，这无疑增加了程序的不稳定性，按以前此时最好的解决办法就是参数改为枚举类型，增加了限定也就提高了稳定性。但是枚举类型就是一把双刃剑，增加安全同时也大大增加了内存占用,尤其是在移动设备上，资源有限更应该注意内存节省。

     谷歌或许考虑到了这些问题，在提供的注解包里添加了注解方式检查类型安全，目前支持int和String两种，看下使用方式：

    ```
    //1、先声明需要的类型常量值
    public static final int TYPE_1 = 1;
    public static final int TYPE_2 = 2;
    //2、创建注解接口同时把上一步声明的常量囊括到这里
    @IntDef({TYPE_1,TYPE_2})
    @Retention(RetentionPolicy.SOURCE)
    public @interface _TYPE{
        
    }
    //3、在函数参数中增加 注解接口名称
    public String getValue(@_TYPE int type){
        switch (type) {
            case 1:
                break;
            case 2:
                break;
        }
        return "";
    }
```

       经过上面的步骤，再调用getValue()函数时如果传入其他int则报错编译不通过，这样通过注解就增加了安全性：

	![屏幕快照 2017-03-29 下午7.13.32.png](http://upload-images.jianshu.io/upload_images/1796052-62fa91a5486d42cb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


结语：基本上APP大部分内存还是被图片占用，处理好图片尤为重要，但是关于图片三级缓存及缩放，目前都使用第三方框架如ImageLoader，所以这里一笔带过。以上就是日常内存优化需要注意的地方，自己做个总结，也希望能对各位看官有所帮助。