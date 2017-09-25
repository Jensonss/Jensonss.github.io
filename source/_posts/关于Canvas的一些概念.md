---
title: 关于Canvas的一些概念
date: 2017-08-03 14:46:20
tags: Android
categories: Android
---

# 前言

在Android开发中不管是图片的绘制还是控件的绘制都少不了**Canvas**的参与。

**Canvas**如此重要，对其多一些了解自然是对开发有益的。

# 获得对象

有两种获得Canvas对象的方法：

- 重写onDraw方法，使用参数中的Canvas
- 直接创建Canvas对象

<!-- more -->

# 它有什么用

Canvas一般译为“画布”。而画布一般只是用来承载画像的，显然在Android中叫Canvas为画布，显然狭隘了。因为Android开发中，Canvas不仅提供了动画特效还提供了常见图形绘制方法，比如圆形、矩形、扇形等。

在进行绘制过程中，还需要借助Pain辅助，Paint可以理解为画笔。



# 绘制图形方法

- 绘制文字

  `drawText(String text, float x, float y, Paint paint)`

  `drawText(char[] text, int index, int count, float x, float y, Paint paint)`

  `drawText(CharSequence text, int start, int end, float x, float y, Paint paint)`

  **float x**：文本开始的x轴位置

  **float y**：文本开始的y轴位置

  **text**：要绘制的文本

  **paint**: 使用的画笔

  **start**：文本开始的位置

  **end**：文本结束的位置

  ​

- 绘制圆形

  `drawCircle(float cx, float cy, float radius, Paint paint)`

  **float cx**：圆心x轴坐标

  **float cy**：圆心y轴坐标

  **float radius**：圆半径

  **paint**：画笔

  ​

- 绘制线条

  `drawLine(float startX, float startY, float stopX, float stopY, Paint paint)`

  **float startX**：起点x坐标

  **float startY**：起点y坐标

  **float stopX**：终点x坐标

  **float stopY**：终点y坐标

  ​

- 绘制椭圆

  `drawOval(float left, float top, float right, float bottom, Paint paint)`

  `drawOval(RectF oval, Paint paint)`

  这里的两个方法可以是一样的，因为绘制一个**RectF**也是需要方法一中的四个点。

  **float left**：左下

  **float top**：左上

  **float right**：右上

  **float bottom**：右下

  ​

- 绘制弧度

  `drawArc(float left, float top, float right, float bottom, float startAngle, float sweepAngle, boolean useCenter, Paint paint)`

  ​

  `drawArc(RectF oval, float startAngle, float sweepAngle, boolean useCenter, Paint paint)`

  **float startAngle**：开始角度

  **float sweepAngle**：扫过角度

  **boolean useCenter**：是否和中心连线 

  ​

- 绘制矩形

  `drawRect(float left, float top, float right, float bottom, Paint paint)`

  `drawRect(Rect r, Paint paint)`

  ​

- 绘制多边形

  多边形绘制需要用到Path，路径类

  用到moveTo，lineTo等常用方法

  `moveTo(float x, float y)`

  `lineTo(float x, float y)`

  ​

- 绘制赛贝尔曲线

  赛贝尔曲线的绘制也用到了Path。

  `quadTo(float x1, float y1, float x2, float y2)`

  ​

- 绘制点

  `drawPoint(float x, float y, Paint paint)`

  `drawPoints(float[] pts, Paint paint)`

  **float x**：点x轴坐标

  **float y**：点y轴坐标

  **float pts**：点数组，每两个值组合一个点，最后如果不够2个点则忽略。

  ​

- 绘制图片

  `drawBitmap(Bitmap bitmap, Matrix matrix, Paint paint)`

  ​

  ​

# 动画方法

- 位移

  `translate(float dx, float dy)`

  **float dx**：x轴移动距离

  **float dy**：y轴移动距离

  ​

- 缩放

  `scale(float sx, float sy)`

  `scale(float sx, float sy, float px, float py)`

  **float sx**：x轴放大倍数

  **float sy**：y轴放大倍数

  **float px, float py**：以该点为中心缩放

- 旋转

  `rotate(float degrees)`

  `rotate(float degrees, float px, float py)`

  **float degrees**：旋转角度

  **float px, float py**：以px，py组成的点为中心旋转degrees角度



# save和restore

- save保存画布

  把save之前的数据保存起来，以便后续的操作在一个新的图层。

- restore合并图层

  把save之前和之后的多个图层进行合并

  ​

  ​



