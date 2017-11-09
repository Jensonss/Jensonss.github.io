
---
title: 代码同时同步到码云、github平台
date: 2017-11-01 16:45:15
tags: 版本控制
categories: 版本控制
---

git允许我们设置多个remote接收push。

`git remote add`添加一个，然后使用`git remote set-url --add`命令添加后续的url。

然后通过`git config --local  —list`命令查看配置的remote是否为多个。

另外码云的项目管理中有个添加远程同步，这个功能是把其他平台代码同步到本地并覆盖。

所以如果单纯是把github的项目同步到码云，这种单向操作也可以使用此功能。

