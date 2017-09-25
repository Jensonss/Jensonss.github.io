---
title: CSS浮动float引起布局错乱
date: 2017-09-12 21:09:42
tags: 前端
categories: 前端
---

# 0x00 前言

html默认是流式布局从上到下，但是使用float后可以让当前标签跳出流式布局限制，但同时下面的标签可能会和float标签产生重叠情况，如下：



![css浮动引起重叠](http://othg5ggzi.bkt.clouddn.com/css%E6%B5%AE%E5%8A%A8%E5%BC%95%E8%B5%B7%E9%87%8D%E5%8F%A0.png)

图中可以看到表单和右边的导航栏重叠了造成布局混乱。

<!-- more -->

# 0x00 解决

如果由CSS浮动造成的布局重叠混乱，可以在浮动标签下新建一个空div：

```html
<div class="clear"></div>
```

给该div设置样式：

```css
 .clear {
            clear: both;
        }
```

最终显示结果如图：

![css浮动错乱修复](http://othg5ggzi.bkt.clouddn.com/css%E6%B5%AE%E5%8A%A8%E4%BF%AE%E5%A4%8D.png)

如果不想使用clear方法也可以使用多个空div+` text-align: right; `属性