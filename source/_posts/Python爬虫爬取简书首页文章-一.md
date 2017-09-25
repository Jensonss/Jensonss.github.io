---
title: Python爬虫爬取简书首页文章(一)
date: 2017-08-02 11:42:28
tags: [Python, 自己写爬虫]
categories: [Python, 自己写爬虫]
---

# 前言

最近对Python兴趣很大，因为觉得这门语言很好玩。学了些东西就总得做点什么出来吧。

**虽然兴趣是最好的老师，但是毕竟实践才是检验真理的唯一标准**

首先想到的就是写个爬虫试试，专门针对特定网站的mini爬虫。

那就采集[简书](www.jianshu.com)首页文章吧。

<!-- more -->

# 准备工作

在开始代码前先做好准备工作：安装所需库。

这里的数据存储在csv文件中，而不是存储在数据库，因为数据流并不大,使用`csv`库。

网络请求库使用`urllib`。

Dom解析库使用`BeautifulSoup4`。

如果本地没有的库可以使用`pip3 install xxx`命令安装。



准备工作做完了就开始编码了吗？NO~，接下来要分析网页，分析要爬取网页的内容。

# 网页分析

分析简书首页文章列表，找到共通的规则，才能让后面的采集有迹可循。

- 打开简书首页，复制一个文章标题

  应该是这样子的：

  ![简书首页标题复制](http://othg5ggzi.bkt.clouddn.com/%E7%AE%80%E4%B9%A6%E9%A6%96%E9%A1%B5%E6%A0%87%E9%A2%98%E5%A4%8D%E5%88%B6.png)

- 查看网页源码，在源码中查找复制的标题

  ![源码中标题区域](http://othg5ggzi.bkt.clouddn.com/%E7%AE%80%E4%B9%A6%E9%A6%96%E9%A1%B5%E6%BA%90%E7%A0%81%E6%98%BE%E7%A4%BA%E6%A0%87%E9%A2%98%E5%8C%BA%E5%9F%9F.png)

  从标题逐步扩散式查找，最终定位到标签`li`。就是说每个首页文章区域部分就是一个`li`标签显示的。而且这些标签的**id**属性都是以**note-**开头的。

- 查找父元素

  既然每个文章是一个`li`标签，那么父元素应该是`ul`标签。顺藤摸瓜发现了

  ```html
  <!-- 文章列表模块 -->
          <ul class="note-list" infinite-scroll-url="/">
  ```

  所以Dom解析时查找**class**为**note-list**子元素即可

**这里首页文章基本理清规则了，其他网站可能复杂性会高，但是只要耐心点，基本都能找到规律**

下面开始创建项目：

# 创建项目

我这里使用Pycharm IDE，创建一个项目名称为`JianShuSpider`。

创建main文件夹，同时在其下创建JS.py文件，该文件主要写逻辑。

创建Article文件，其中创建Article类，用来存储爬取的文章信息。



# 开始实现

## 确定爬取内容

因为这里只是个练习爬虫，不想存储太多内容，所以只爬取文章标题、作者、链接

完善Article类：

```python
class Article:
    """
        文章信息类
    """
    def __init__(self):
        self.title = ''
        self.author = ''
        self.url = ''

```

## 逻辑实现

总体逻辑大概分成三个方法：

①定义`def getArticles(url):`方法获取文章列表

②定义`def saveCSV(articles):`方法存储获取的文章列表信息

③定义`def wrapper():`包装上述两个方法以便在让工作线程执行

下面按次序一次实现

### 获取文章列表

获取文章列表又细分为4个小步骤：

①定义一个空列表用来后面存储文章信息：

```python
article_list = []
```

②发起网络请求读取网页源码，为后面DOM解析做准备，如果出现异常返回空列表

```python
from urllib import error, request
 try:                                      
     req = request.urlopen(url=url)        
     content = req.read()                  
 except error.HTTPError as  err:           
     print(err)                            
     return article_list                   
```

③根据步骤②提供的content DOM树和上面分析li标签规则，通过正则表达式查找当前页所有文章标签li：

```python
 import bs4
 import re

 bsobj = bs4.BeautifulSoup(content, "html.parser")
    list = bsobj.find_all(name="li",
                          attrs={"id": re.compile("^(note-)[0-9]+$")})
```

④根据步骤③返回的li标签列表，对li列表标签进行遍历查找子标签中标题、作者和链接信息，赋值给Article对象，并添加到步骤①创建的**article_list**列表

```python
 for item in list:                                                                 
     assert isinstance(item, bs4.Tag)                                              
     article = Article()                                                           
     article.author = item.find(name='a', attrs={'class': 'blue-link'}).get_text() 
     titleTag = item.find(name='a', attrs={'class': 'title'})                      
     assert isinstance(titleTag, bs4.Tag)                                          
     article.title = titleTag.get_text()                                           
     article.url = url + titleTag.attrs['href']                                    
     article_list.append(article)                                                  
```

经过上面一些列步骤已经得到了文章列表信息，下面要把文章列表存储到csv文件中

### 存储文章列表

**注意：csv是按逗号分隔的文件，每一行代表一条完整信息，所以写入信息时是逐行写入。**

存储文章可以分为3个小步骤：

①根据path打开或创建一个csv文件，同时获取writer

```python
import csv
 csvFile = open("/Users/jenson/Downloads/js.csv", 'w+')    
 csv_writer = csv.writer(csvFile)                          
```

②向文件写入头行，用来说明每个被逗号隔断的数据表示的意义

```python
   csv_writer.writerow(["作者", "标题", "链接"])   
```

③遍历文章列表存储

```python
for article in articles:                               
    csv_writer.writerow(                               
        [article.author, article.title, article.url])  
```



### 包装方法

包装方法就简单了，直接上代码：

```python
def wrapper():    
    """           
    包装方法          
    :return:      
    """           
    list = getArti
    saveCSV(list) 
```



### 线程调用

```python
threading.Thread(target=wrapper).start()

```

# 爬取结果

![爬取结果](http://othg5ggzi.bkt.clouddn.com/%E7%AE%80%E4%B9%A6%E9%A6%96%E9%A1%B5%E7%AC%AC%E4%B8%80%E9%A1%B5%E6%96%87%E7%AB%A0%E7%88%AC%E5%8F%96%E7%BB%93%E6%9E%9C.png)



通过上面的图片可以看到文章信息已经存储到csv文件中。

目前为止，一个mini爬虫已经初步完成。当然目前只是爬取第一页信息。

后面文章会完成分页爬取，请参考**Python爬虫爬取简书首页文章(二)**

