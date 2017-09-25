---
title: Android内存分析工具之MAT详解
date: 2017-04-27 13:07:09
tags: Android
categories: Android
---

# 说在前面的话

关于Android内存优化可参见我的另一篇文章[[Android性能优化之内存优化](http://www.jensondev.me/2017/04/21/Android%E6%80%A7%E8%83%BD%E4%BC%98%E5%8C%96%E4%B9%8B%E5%86%85%E5%AD%98%E4%BC%98%E5%8C%96/)]。Android性能优化中内存优化可谓重中之重，而内存泄露又是Android内存优化的重灾区。重灾区就要重拳出击，整治内存泄露，Mat是必备的利器。

<!-- more -->

# 关于Mat



Mat，全面Memory Analyzer Tool，Java内存分析工具。注意一点，Mat是由eclipse社区维护，所以如果使用Android studio开发的话，需要单独下载，[官方通道](http://www.eclipse.org/mat/downloads.php)。我用的是mac版，后面也会使用mac版来分析。

# Mat使用

注意：我的测试app包名为：com.example.jenson.myapplication

在eclipse中集成了Mat，可以直接拿来使用，而使用Android studio时。这两个是分开的，所以我们要先导出dump文件。

## Android studio导出dump文件

打开Android Monitor窗口，![0B8D42B7-E5A5-431D-9658-2A99C923CF4E](Android内存分析工具之MAT详解/0B8D42B7-E5A5-431D-9658-2A99C923CF4E.png)

点击圆圈中的按钮，过几秒中会自动生成一个dump文件，然后自动打开如下图的窗口：![1A94CD89-1BE9-46BC-9449-8B55EE34D741](Android内存分析工具之MAT详解/1A94CD89-1BE9-46BC-9449-8B55EE34D741.png)

在Heap Snapshot下的几个文件就是自动生成的dump文件，但是这不是标准的dump文件，所以需要选择文件右键导出标准dump文件，保存到一个目录下：

![屏幕快照 2017-04-27 下午1.49.26](Android内存分析工具之MAT详解/屏幕快照 2017-04-27 下午1.49.26.png)



## Mat打开标准dump文件

使用Mat，菜单open File选择刚才导出的dump文件，经过分析后回显示如下所示的项目：

![屏幕快照 2017-04-27 下午2.08.27](Android内存分析工具之MAT详解/屏幕快照 2017-04-27 下午2.08.27.png)



Mat从多角度提供了内存分析，其中包括Histogram、Dominator Tree、Leak Suspects和Top consumers等。

overview是一个概况图，把内存的消耗以饼状图形式显示出来，鼠标在每个饼块区域划过或者点击，就会在Inspector栏目显示这块区域的相关信息。

Leak Suspectss是一个关于内存泄露猜想的饼图，Problem Suspect 1是泄露猜想的描述。看我这里说的像是字节数组，这么大的数组难不成是图片，要知道图片在内存中就是以字节数组形式存在的。

平常内存分析比较常用的还是Histogram、Dominator Tree。

## 开始分析内存

这里我们使用Histogram进行分析，切换到Histogram页面。这个页面主要有4个列，Class Name、Objects、Shallow Heap和Retained Heap。其中Class Name是全类名，Objects是这个类的对象实例个数。Shallow Heap是对象本身占用内存大小，非数组的常规对象，本身内存占用很小，所以这个对泄露分析作用不大。Retained Heap指当前对象大小和当前对象能直接或间接引用的对象大小的总和。这个栏目是分析重点。

内存分析是分析的整个系统的内存泄露，而我们只要查找我们APP的内存泄露情况。这无疑增加了很多工作，不过幸亏Histogram支持正则表达式查找，在Regex中输入我们的包名进行过滤，直奔和我们APP有关的内存泄露：

![屏幕快照 2017-04-27 下午3.11.33](Android内存分析工具之MAT详解/屏幕快照 2017-04-27 下午3.11.33.png)

过滤后就显示了我们APP相关内存信息，按Retained Heap大小排列下，发现Student和MainActivity这两个类问题比较大。但是MainActivity的Objects数量为1，而Student的为130，看起来Student问题更严重，所以先从Student入手：

首先看下是哪里的引用导致了Student不能被GC回收。![屏幕快照 2017-04-27 下午3.19.06](Android内存分析工具之MAT详解/屏幕快照 2017-04-27 下午3.19.06.png)

右键使用Merge Shortest Paths to GC Roots显示距GC Root最短路径，当然选择过程中要排除软引用和弱引用，因为这些标记的一般都是可以被回收的。进入结果页查看：

![屏幕快照 2017-04-27 下午3.22.23](Android内存分析工具之MAT详解/屏幕快照 2017-04-27 下午3.22.23.png)

啧啧，原来Student不能被回收竟然就是这个MainActivity搞的鬼，可以看到这个类里面有个students集合里面存放的正式不能回收的Student对象。

看代码：

```
    static List<Student> students=new ArrayList<>();
    static final String tag="MainActivity";
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        sample_text = (TextView) findViewById(R.id.sample_text);
        TestNative testNative = new TestNative();
        sample_text.setText(testNative.stringFromJNI());
        for (int i = 0; i < 10; i++) {

            Student student = new Student("jenson" + i, i);
            students.add(student);
        }
    }
```

原来不能被回收是因为用static修饰了students集合。这导致其生命周期边长，在Activity销毁时，仍然存在。问题找到了，去掉static完成内存泄露修复。

