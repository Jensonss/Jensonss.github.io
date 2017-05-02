---
title: osx下如何使用SublimeText阅读Android系统源码
date: 2017-04-22 18:55:54
tags: 工具
categories: 工具
---

>以前用win系统时使用sourceinsight阅读源码方便至极，但是自从换到mac下，尝试了很多方法很多工具，比如understand、比如vscode、比如wine下安装sourceinsight。总是有各种问题，所以这次尝试使用SublimeText

平时使用Sublimetext都是打开单个文件，这次要导入几万个文件，这在一些ide中称为project。同样在Sublime中也是如此。

#### 1. 创建工程

　　Project > Add Folder to Project 选择源码目录即可
　　这时在窗口左边栏中将出现刚刚添加的文件目录，如果还需要添加其他目录，则重复这一操作即可。

#### 2. 保存工程

　　Project > Save Project As
　　点击保存后Sublime Text将自动生成两个文件：
project_name.sumlime-project：包含工程定义，该文件会被记录到版本控制里。
project_name.sublim-workspace：包含了用户的工程数据，例如打开的文件和修改等，该文件不会被记录到版本控制里。
　　在*.sublime-project文件中，你可以定义工程配置。例如你可以定义多个目录路径，或者哪些目录或文件需要排除在外。例如：
　　以上*.sublime-project文件中定义了两个目录，而“name”标签是该目录在Sublime Text的左边栏中显示的名字。通过定义“name”，你可以将一些长路径或者名字很长的目录以更简洁的名称显示在左边栏中。

#### 3、添加索引

- 安装Package Control插件
  1.打开控制台
       方法1：View->show console 
       方法2：ctrl+~
    2.输入代码：
  python2.x代码:

```
import urllib2,os;pf='Package Control.sublime-package';ipp=sublime.installed_packages_path();os.makedirs(ipp) if not os.path.exists(ipp) else None;open(os.path.join(ipp,pf),'wb').write(urllib2.urlopen('http://sublime.wbond.net/'+pf.replace(' ','%20')).read())  
```
python3.x代码：

```
import urllib.request,os;pf='Package Control.sublime-package';ipp=sublime.installed_packages_path();os.makedirs(ipp) if not os.path.exists(ipp) else None;open(os.path.join(ipp,pf),'wb').write(urllib.request.urlopen('http://sublime.wbond.net/'+pf.replace(' ','%20')).read())
```
- 安装CTags
  - 1. 首先 Preference -> Browser Packages... 查看是否已经安装了CTags Package，如果没有则继续下面步骤
  - 2. Preference -> Package control, 输入“install package”，它会找出你可以安装的插件，在列表中选择ctag插件进行安装

   - 3.创建索引
  
![屏幕快照 2017-03-22 下午2.55.23.png](http://upload-images.jianshu.io/upload_images/1796052-38f35a1199d9e85a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 4、异常处理

- 期间出现Package Control:There are no packages available for installation弹窗提示
选择Preference->Browser Packages打开文件夹后
删除Package Control目录、删除User目录下相关Package Control名称文件等，再次安装。
- ctag rebuild时弹窗异常如图：

![屏幕快照 2017-03-22 下午3.01.14.png](http://upload-images.jianshu.io/upload_images/1796052-ad6b9e4b667a7b5b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这是因为我们还没有安装和配置ctags
下载ctags-5.8.tar.gz解压并编译，依次执行如下命令：
tar xzvf ctags-5.8.tar.gz
cd ctags-5.8
./configure
make
sudo make install
打开Preference ->Package settings->ctags->settings-user和settings-default
把default中的配置全部复制到user中，然后改一下command配置项，为ctags的可执行文件路径/usr/local/bin/ctags（可以通过which ctags命令查看ctags路径）
#### 5、快捷键
- 修改跳转
默认函数跳转：Ctrl+shift+左键; 跳转返回：Ctrl+shift+右键。修改方法：
 Perference->Package Settings->CTags->Mouse Binding Default->复制到Mouse Binding User，把里面的"ctrl+shift"，修改为“command”，这样就可以用“command+左键”跳转了：

```
        Command+P：查找文件
        Command+R：查找方法
        Command+左键：文件或函数跳转        
        Command+右键：返回文件或函数跳转的原始位置
```

至此Android源码项目创建完成并创建索引，代码跳转也反应迅速。