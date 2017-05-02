---
title: osx下pip3安装matplotlib时 The following required packages can not be built freetype
date: 2017-04-22 18:55:54
tags: Python
categories: Python
---
'The following required packages can not be built: * freetype'
出现这个提示，乍一看以为没有安装freetype的原因，后来找其安装方法。使用brew install freetype  ，有提示already installed。然后又看了很多文章，照着改了很多都没有办法。后来胡乱一通查找看到了这个[问答](http://stackoverflow.com/questions/12363557/matplotlib-install-failure-on-mac-osx-10-8-mountain-lion)
```
I think the other answers are on the right track, but I encountered this same problem and can attest that:
brew install pkg-config
brew install freetype
pip install matplotlib
```
上面意思说这哥们遇到过同样的问题，下面的能解决。所以试了下```brew install pkg-config```然后自动安装了，因为freetype已经提示安装了。最后又安装了一次```pip3 install matplotlib```成功了