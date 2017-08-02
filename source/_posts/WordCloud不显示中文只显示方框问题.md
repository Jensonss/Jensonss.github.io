---
title: WordCloud不显示中文只显示方框问题
date: 2017-07-29 18:59:36
tags: [Python, WordCloud]
categories: [Python, WordCloud]
---

# 前言

在用词云展示时，发现其他能正常运行，但是启动后的图像中都是方框，后来查找到原因是因为词云默认只支持英文。

如果要支持中文需要自己设置支持中文的字体

# 解决方案

```python
my_wordcloud = WordCloud(font_path='/Library/Fonts/Songti.ttc').generate(wl_space_split)

```

手动设置宋体字体，最终就能正常显示了