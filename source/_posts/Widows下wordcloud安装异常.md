---
title: Widows下wordcloud安装异常
date: 2017-08-18 17:08:04
tags: Python
categories: Python
---

# 0x00 前言

在windows系统下安装wordcloud库时提示异常信息：

```
  building 'wordcloud.query_integral_image' extension
    error: Microsoft Visual C++ 14.0 is required. Get it with "Microsoft Visual C++ Build Tools": http://landinghub.visualstudio.com/visual-cpp-build-tools
```

又缺少依赖，win下开发真是毛病多

# 0x01 解决

从[这里下载](http://www.lfd.uci.edu/~gohlke/pythonlibs/#wordcloud)。所需的wordcloud模块的whl文件，

下载后进入存储该文件的路径，执行“pip install wordcloud-1.3.2-cp36-cp36m-win_amd64.whl”，这样就会安装成功。

```
Installing collected packages: wordcloud
Successfully installed wordcloud-1.3.2
```

