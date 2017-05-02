---
title: osx如何安装Homrbrew
date: 2017-04-22 18:55:54
tags: Python
categories: Python
---
- 1.homebrew依赖于xcode，所以在终端先执行 ```xcode-select --install``` 期间弹窗一直确定即可，根据网络速度不同，可能稍有延迟

- 2.上一步完成了，终端继续输入
```ruby -e "$(curl -fsSLhttps://raw.githubusercontent.com/Homebrew/install/master/install)"```直到看到终端中显示install successful字样
- 3.执行执行```brew help```看是否有提示
