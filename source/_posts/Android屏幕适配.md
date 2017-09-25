---
title: Android屏幕适配
date: 2017-08-01 13:23:03
tags: Android
categories: Android
---

# 前言

由于Android手机尺寸、分辨率碎片化严重，在Android应用开发中屏幕适配需求也变得越来越重要。

#  概念理解

**屏幕尺寸**指屏幕对角线的长度，以英寸为单位，1英寸=2.54厘米。
**屏幕分辨率**指在横纵向上的像素点数，单位是px，1px=1像素点，一般是纵向像素x横向像素，如1280×720。
**屏幕像素密度**是指每英寸上的像素点数，单位是dpi，即“dot per inch”的缩写，像素密度和屏幕尺寸和屏幕分辨率有关，像素密度也叫dpi。

**dp，也是dip：**Density Independent Pixels的缩写。以**160dpi**为基准，1dp=1px，密度无关像素
**dpi：**屏幕像素密度的单位，“dot per inch”的缩写

**px：**像素，物理上的绝对单位

**sp：**Scale-Independent Pixels的缩写，可以根据系统自动进行缩放。通常可以使用12sp，14sp，18sp，22sp，为了避免精度失真一般不使用奇数和小数。

<!-- more -->

不同密度区分：

| 名称      | 密度范围        | 图片大小      | 比例   |
| ------- | ----------- | --------- | ---- |
| mdpi    | 120dp~160dp | 48×48px   | 2    |
| hdpi    | 160dp~240dp | 72×72px   | 3    |
| xhdpi   | 240dp~320dp | 96×96px   | 4    |
| xxhdpi  | 320dp~480dp | 144×144px | 6    |
| xxxhdpi | 480dp~640dp | 192×192px | 8    |
|         |             |           | 12   |

**如果只想出一套图片优先出xxhdpi，这样在低于这个密度下的系统会压缩图片进行显示，不至于会模糊，同时由于xxxhdpi还没有流行起来，用户数量少。**



# 适配方法

## 适配尺寸

- 优先使用wrap_content，matc_parent，weight

- 使用相对布局，禁止使用绝对布局

- 使用限定符

  - 尺寸限定符，如layout-large

    3.2系统以前有用，

    3.2版本后又退出了最小宽度限定符如layout-sw600dp

    sw=small width 最小宽度。

    如果要维护全系统版本，需要维护的布局太多，应该抽取共同布局

    创建`res/layout/main.xml`布局文件。

    默认布局`res/values/layout.xml`。内容为：

    ```xml
    <resource>
    	<item name="main"type="layout">
          @layout/main
        </item>
      </resource>
    ```

    创建`res/layout/main_twopanels.xml`布局文件。

    给3.2之前版本创建`res/values-large/layout.xml`文件，内容为：

    ```xml
    <resource>
    	<item name="main"type="layout">
          @layout/main_twopanels
        </item>
      </resource>
    ```

    给3.2之后的版本创建`res/values-sw600dp/layout.xml`文件，内容为：

    ```xml
    <resource>
    	<item name="main"type="layout">
          @layout/main_twopanels
        </item>
      </resource>
    ```

    ​

  - 屏幕方向限定符：-land，-port

- 使用点九位图

  左上控制拉伸区域，右下控制间隔区域



## 适配密度

- 使用非密度制约像素  如dp，sp

  ​

- 多种位图

  **如果只提供一种高分辨率图，将其放在对应的密度drawable下没有问题。但是如果将其放入低于当前密度的drawable下，会造成图片按比例放大，导致内存倍数增加**

  比如把一个1920x1080的xxhdpi的图片放在mdpi下，内存占用会增加9倍。

  按照图片比例的2：3：4：6：8计算出来。

  ​