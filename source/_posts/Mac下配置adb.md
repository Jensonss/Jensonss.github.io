---
title: Mac下配置adb
date: 2017-07-13 21:10:42
tags: Android
categories: Android
---

# 0x00 前言

Mac和windows一样，如果要在终端命令行全局使用非系统命令，需要手动把命令环境配置到指定文件中，具体操作就是把命令文件所属路径配置到.bash_profile里面。

# 0x01 分别找到双方路径

## 找到adb

我是通过Android studio中的idk location找到SDK位置`/Users/jenson/Library/Android/sdk` .



<!-- more -->

## 找到.bash_profile

- 使用命令行`cd $home` 进入home路径下，

- 因为mac下以点开头的文件为隐藏文件，所以使用`ls -al` 查看所有文件，查看列表是否有.bash_profile文件，

- 如果没有，使用`touch .bash_profile` 创建，如果有则执行命令`open -e .bash_profile` 使用文本编辑器打开文件，

- 在下面按格式添加SDK路径：

  ```
  export PATH=/Users/jenson/Library/Android/sdk/platform-tools:$PATH
  export PATH=/Users/jenson/Library/Android/sdk/tools:$PATH
  ```

  ​

## open -e .bash_profile提示open命令不存在

通过Finder根据路径找到.bash_profile文件，编辑添加

```
export PATH="/usr/bin:/bin:/usr/sbin:/sbin"
export PATH="/usr/local/bin:/usr/local/sbin:$PATH"
```

