---
title: No resource identifier found for attribute 'roundIcon' in package 'android'
date: 2017-08-04 11:37:39
tags: Android
categories: Android
---

# 前言

Android studio更新到2.3后创建项目时就出现了这个问题，我只要降低版本就出现编译异常信息：

`No resource identifier found for attribute 'roundIcon' in package 'android'Ask Question`。

这个**roundIcon**真是害人匪浅。

而且也是感受到了天下文章一大抄的说法所言非虚。。

只要搜出来的说法都是删掉`roundIcon`那个属性即可。

可是我和其他网友都遇到了另一个问题：删除后再编译又会自动给添加上，而编译版本已经改为了5.0。

# 解决

- 简单做法

如果只是因为没有为最新系统功能做好适配工作。那么可以把**targetSdkVersion**降回到所需版本，但保留**compileSdkVersion 26**编译版本最新。这是最简单做法，但是可能有一些过时API需要进行版本判断。

- 我还想降低compileSdkVersion为20

  当把**compileSdkVersion**降为20的时候，再次编译项目就出现了`Error:(11) No resource identifier found for attribute 'roundIcon' in package 'android'`。

  这时想到还有构建版本还是26.0.0，然后也改为20.0.0了。再次编译

  这时候仍然有异常，但是信息变了：`Error:The SDK Build Tools revision (20.0.0) is too low for project ':app'. Minimum required is 25.0.0
  <a href="fix.build.tools.version">Update Build Tools version and sync project</a><br><a href="openFile:/Users/jenson/AndroidStudioProjects/CanvasTest/app/build.gradle">Open File</a>`。

  这是一个好兆头，说明我们的对手变了。我现在要做的就是让项目认可低版本的bt(build tool)。

  这条异常是由：Gradle sync发出的。所以异常和Gradle有关，那现在问题矛头指向Gradle，而Gradle又有Gradle版本和插件版本，

  **会不会是版本太新缘故，毕竟谷歌老喜欢搞新花样，所以现在的想法是回退到一个合适的版本。**
  目前版本是**gradle-3.3-all**和**com.android.tools.build:gradle:2.3.3**

  现在使用**gradle-3.0-all**和**com.android.tools.build:gradle:2.2.0**

  从新编译时出现了如下弹窗：

  ![gradle版本升级弹窗](http://othg5ggzi.bkt.clouddn.com/gradle%E7%89%88%E6%9C%AC%E5%8D%87%E7%BA%A7%E6%8F%90%E7%A4%BA%E5%BC%B9%E7%AA%97.png)



​	说明两个版本在匹配范围内，获得初步成功，但是编译完成时再次提示了**roundIcon**，然后这次把**属性删掉**，同时**clean project**。

然后又出现了如下异常信息：

Error:In <declare-styleable> ConstraintSet, unable to find attribute android:elevation

这可能是由于把compileSdkVersion设置20，有点太低了得缘故。

既然如此，干脆一不做二不休，进入布局文件把**android.support.constraint.ConstraintLayout**布局替换为其他布局。同时删除该类用到的一些属性，譬如：

```
app:layout_constraintBottom_toBottomOf="parent"
app:layout_constraintLeft_toLeftOf="parent"
app:layout_constraintRight_toRightOf="parent"
app:layout_constraintTop_toTopOf="parent"
```

斩草要除根，把依赖包也干脆去掉：

删除**compile 'com.android.support.constraint:constraint-layout:1.0.2'**



最最最后clean project，各种build后没有终于恢复正常了。。。。



> 提示：每次升级前注意下当前Gradle版本是多少，对应的Plugin版本又是多少，如果升级后有问题，可以方便的回退。



gradle本地：

```
distributionUrl=file:/Users/jenson/.gradle/wrapper/dists/gradle-3.0-all.zip
```