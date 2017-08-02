---
title: Selenium ChromeDriver安装
date: 2017-08-02 02:57:02
tags: [Python, 自动化]
categories: [Python, 自动化]
---

# 前言

Selenium默认自带了Firefox的驱动，所以如果要使用该框架，要么自己安装Chrome或IE驱动，要么安装FireFox浏览器。

因为我习惯使用Chrome，所以要手动安装驱动。

# 驱动下载

[ChromeDriver](https://sites.google.com/a/chromium.org/chromedriver/home)下载地址：https://chromedriver.storage.googleapis.com/index.html?path=2.31/

# 使用

mac端下载完成解压后可直接使用，不过要在代码中声明解压后的文件路径，如：

```python
browser = webdriver.Chrome(executable_path="/Users/jenson/Downloads/chromedriver")
browser.get("http://www.jianshu.com")    
```









#  