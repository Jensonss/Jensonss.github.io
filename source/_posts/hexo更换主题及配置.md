---
title: hexo更换主题及配置
date: 2017-07-14 10:42:33
tags: 网络
categories: 网络
---

# 0x00 前言

看惯了next样式，这几天想换一款主题换个心情。挑来挑去发现[yilia](https://github.com/litten/hexo-theme-yilia)主题不错，左侧导航和个人信息，还能带头像。so接下来就开始主题切换流程。

# 0x01 下载

- 切换到你的博客根目录下

  如我的是这样子：`cd ~/Downloads/hexo/` 

- 下载前进行源文件备份

  首先`hero d -g`把当前博客从新部署，

  然后把源文件全部提交：`git add .`添加临时，`git commit -m "全部备份"`提交本地，`git push origin xxx`push到服务器。

- 在根目录下执行clone

  命令：`git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia`

# 0x02 安装

完成了下载和备份，接下来开始主题切换，在未确认完全完成之前不要执行`hexo d -g`部署到服务器，先在本地测试。

- 切换到主题

  打开博客根目录下的`_config.yml`文件，找到`theme: next`栏目把next改为`yilia`后保存。如下：

  ![换主题](/Users/jenson/Downloads/hexo/source/_posts/hexo更换主题及配置/换主题.png)

  ​

- 运行本地测试

  执行` hexo s -debug`启动本地测试服务，打开`http://localhost:4000/`页面发现主题已经切换过来了，切换就是这么简单，但是还有很多地方要配置。

# 0x03 配置

这里的配置如果没有特殊说明，默认都是`themes/yilia/_config.yml`文件的配置

- menu配置

  默认menu中只有主页和相册，但是我不想要相册，同时还想添加特定的栏目。

  相册不要只要注释掉就好了，但是如何添加特定栏目呢？我们在写文章时头部文件都有类似的格式：

  ```
  title: hexo更换主题
  date: 2017-07-14 10:42:33
  tags: 网络
  categories: 网络
  ```

  其中tags和categories都是可以在menu中使用的，比如我想添加Android菜单，只要设置`Android: /tags/Android`即可，前提是我之前的文章中已经存在tags为Android的标签了。当然如果设置categories也可以的，只要之前有设置过这个类型即可，比如`Java: /categories/Java`。我的配置如下：

  ![menu配置](/Users/jenson/Downloads/hexo/source/_posts/hexo更换主题及配置/menu配置.png)

  这样在点击Android菜单后，右侧都是Android类型的文章列表。

- subnav配置

  subnav主要是个人其他站点相关页面链接，如果没有则直接注释掉，但是为了UI好看还是留了三个：

  ```
  # SubNav
  subnav:
    #github: "#"
    #weibo: "#"
    #rss: "#"
    #zhihu: "#"
    #qq: "840418528"
    #weixin: "#"
    jianshu: "http://www.jianshu.com/u/1b5cb41384f4"
    #douban: "#"
    #segmentfault: "#"
    #bilibili: "#"
    #acfun: "#"
    mail: "mailto:songjlforever@foxmail.com"
    #facebook: "#"
    google: "https://www.google.com"
    #twitter: "#"
    #linkedin: "#"
  ```

  ​

- 头像配置

  在source下新建一个文件夹起个名字比如：image。头像就是一个图片为什么还要单独建个文件夹？因为下面的打赏里面还要微信和支付宝两个二维码图片！将头像图片拷贝到image目录中，在配置文件中找到`avatar:`修改如下：

  ```
  #你的头像url
  avatar: /image/avatar.jpg
  ```

  刷新`http://localhost:4000/`发现头像已经出现了。

- 打赏配置

  把支付宝和微信收款二维码拷贝到上面创建的image文件夹。然后在配置文件中修改如下：

  ```
  # 打赏
  # 打赏type设定：0-关闭打赏； 1-文章对应的md文件里有reward:true属性，才有打赏； 2-所有文章均有打赏
  reward_type: 2
  # 打赏wording
  reward_wording: '谢谢您的支持!'
  # 支付宝二维码图片地址，跟你设置头像的方式一样。比如：/assets/img/alipay.jpg
  alipay: /image/alipay.jpg
  # 微信二维码图片地址
  weixin: /image/weixin.png
  ```

  显示效果如图：

  ![打赏效果图](/Users/jenson/Downloads/hexo/source/_posts/hexo更换主题及配置/打赏效果图.png)

  ​

  ​

- 关于我配置

  smart_menu中有关于我的menu，点击后只显示配置文件中的`aboutme: 一个从运维转战开发的程序猿。`这里内容格式样式太少，我想像next主题中关于我是个单独的页面，那样看起来更舒服。

  当然可以实现，

  首先注释掉`smart_menu`中的`aboutme`。

  然后在配置文件顶部`menu`中添加`关于我: /about/`。这是刷新UI后点击关于我，打开新的页面如下：

  ![关于我](/Users/jenson/Downloads/hexo/source/_posts/hexo更换主题及配置/关于我.png)

  ​

- 点击所有文章提示模块缺失

  问题如下：

  ![模块缺失](/Users/jenson/Downloads/hexo/source/_posts/hexo更换主题及配置/模块缺失.png)

  首先确保node版本在6.2以上，我的是6.10.2仍然出现这个问题。

  然后在博客根目录下执行`npm i hexo-generator-content --save`

  最后在根目录中的`_config.yml`里添加如下：

  ```
  jsonContent:
    meta: false
    pages: false
    posts:
      title: true
      date: true
      path: true
      text: false
      raw: false
      content: false
      slug: false
      updated: false
      comments: false
      link: false
      permalink: false
      excerpt: false
      categories: false
      tags: true
  ```

  > 注意这段内容格式，尤其是多余空格，否则可能引发其他错误

