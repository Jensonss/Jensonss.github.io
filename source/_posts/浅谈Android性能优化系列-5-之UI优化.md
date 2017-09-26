---
title: 浅谈Android性能优化系列(5)之UI优化
date: 2017-09-26 18:30:30
tags: Android
categories: Android
---

# 0x00 前言

UI的现实在应用层需要经过测量、布局、绘制三步，每一步都会耗费一定时间，如果UI繁重，导致耗费时间过多造成画面卡顿，造成不好的用户体验。研究显示，0-100ms的延迟会让用户感知到瞬时的卡顿，100-300ms的延迟会让用户感觉迟缓，300-1000ms的延迟让用户感觉“手机卡死了”，1000ms以上的延迟会让用户想要去干别的事情，由此可见有良好的UI优化习惯很重要。

# 0x01 优化到什么程度

如今屏幕刷新频率大都是60FPS，就是说每帧绘制只要16ms，即保证你的UI页面在16ms内绘制渲染完成，就会让用户感觉到体验是流畅的，所以我们需要做的就是确保我们的APP页面渲染小于16ms。

<!-- more -->

# 0x02 如何优化

## 布局优化

### 减少xml布局层级

### Merge的使用

Merge是合并的意思，使用Merge合并子元素和父View，而Merge本身可以被忽略。使用Merge的场合：

xml布局中，根元素是FrameLayout时；

自定义View中，父元素尽量是FrameLayout或者LinearLayout；

>Merge不能乱用：
>
>Merge只能用在xml布局根元素；
>
>使用Merge加载一个布局时，必须制定一个ViewGroup作为其父元素，并且设置attachToRoot参数为True（inflate(int,ViewGroup,boolean)）；
>
>不能在ViewStub中使用Merge标签，原因就是ViewStub的inflate方法中没有attachToRoot的设置

### 合理使用RelativeLayout和LinearLayout

RelativeLayout一定程度上可以减少布局层级，但是其对子View测量次数多于LinearLayout。所以综合考虑：如果层级较多情况下，使用RelativeLayout能减少层级的话，优先使用RelativeLayout以便保持界面扁平化；如果层级相同的情况下优先使用LinearLayout，这样能减少子View多次测量。

### ViewStub提高加载速度

ViewStub默认不可见不占位置，

如果在特定情况下才显示某些布局，可以使用ViewStub。

显示ViewStub有两种方法：ViewStub.inflate()和ViewStub.setVisibility(View.Visible)；

> 使用ViewStub注意：
>
> Viewstub只能加载一次，之后该对象引用会被置空；
>
> Viewstub只能用来加载一个布局文件，而不是某个View
>
> Viewstub中不能嵌套Merge

### include实现view复用

对于在多个页面都会使用的公共布局诸如Title栏或导航栏，提取出来通过使用inlucde引入，这样只需要维护一份代码即可。

## 避免过度绘制

### 什么是过度绘制

过度绘制是说屏幕上某一像素在同一帧时间内被绘制多次。在UI布局中如果不可见的部分UI也在进行绘制，这会导致浪费多余的CPU和GPU资源。

### 引起过度绘制原因

xml布局中控件重叠且都设置了背景或图片

自定义View，onDraw方法中同一区域绘制了多次

### 如何避免过度绘制

- 布局优化

  移除xml中非必须背景，或根据条件设置

  移除window默认背景

  按需设置占位背景图

- 自定义View优化

  在自定义 V i e w中可以通过 c a n v a s . c l i p R e c t （ ）来帮助系统识别那些可见的区域 。这个方法可以指定一块矩形区域 ，只有在这个区域内才会被绘制 ，其他的区域会被忽视 。 c a n v a s . c l i p R e c t （ ）可以很好地帮助那些有多组重叠组件的自定义 V i e w来控制显示的区域 。 c l i p R e c t方法还可以帮助节约 C P U与 G P U资源 ，在 c l i p R e c t区域之外的绘制指令都不会被执行 ，那些部分内容在矩形区域内的组件 ，仍然会得到绘制 ，并且可以使用 c a n v a s . q u i c k r e j e c t （ ）来判断是否没和某个矩形相交 ，从而跳过那些非矩形区域内的绘制操作 。

## 合理使用动画

动画对于提高视觉感官舒适度很有帮助，

对于IO等耗时操作给予适当的动画提示在一定程度上提高用户体验。