---
title: Python入门系列(1):如何使用Sublime text开发Python
date: 2017-04-22 18:55:54
tags: Python
categories: Python
---
# 下载并安装Sublime text3
点击下载[osx版](https://download.sublimetext.com/Sublime%20Text%20Build%203126.dmg)直接安装即可。
如果需要其他系统版本请移步[这里](http://www.sublimetext.com/3)
安装完成，在launcher启动Sublime

![屏幕快照 2017-01-06 下午8.32.28.png](http://upload-images.jianshu.io/upload_images/1796052-c2bc5a099511d506.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 配置Python3的编译环境
-  找到Python3的安装路径
终端中输入命令

``` type -a python3 ```

可以看到结果

![屏幕快照 2017-01-06 下午8.37.07.png](http://upload-images.jianshu.io/upload_images/1796052-01e91620f87d697b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

复制路径```/Library/Frameworks/Python.framework/Versions/3.5/bin/python3```

- 配置Sublime
  打开Sublime->Tools->build system->new build system 
如下图可以看到列表里面已经有了Python编译环境，但这是针对Python2.x的版本的，需要我们手动添加3.x版本。

![屏幕快照 2017-01-06 下午8.41.06.png](http://upload-images.jianshu.io/upload_images/1796052-8353a6ec526679ac.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击new build system 后的窗口如下图：

![屏幕快照 2017-01-06 下午8.46.29.png](http://upload-images.jianshu.io/upload_images/1796052-e855b34a3025e14a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将其修改为
```
{
"cmd": ["之前得到的python3路径","-u","$file"],
}
```

然后点击save as保存为```Python3.sublime-build```
此时再次查看build system列表发现Python3已经出现了，至此Sublime的Python3编译环境已经完成。

![屏幕快照 2017-01-06 下午8.51.22.png](http://upload-images.jianshu.io/upload_images/1796052-81e3e153c615759e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 开始我们的第一个程序hello Python！
- 新建工作目录
桌面新建一个名为python_work的文件夹，用来存放python文件
- 新建文件
使用Sublime-->new file 新建一个空文件保存到刚才创建的python_work中，命名为hello_python.py 。(*这里命名时加了.py后缀是告诉Sublime说我这个文件是python程序，这样在编写时候会给出关键字的颜色标示，并且 编译时候会使用python编译环境*)

- 打印hello python

```
print("hello python!")
```

编写完代码保存，command+B执行编译

![屏幕快照 2017-01-06 下午9.03.31.png](http://upload-images.jianshu.io/upload_images/1796052-a4f30b1ba7ac0f9e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

本节完成。