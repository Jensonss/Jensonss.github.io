---
title: Python爬虫爬取简书首页文章(二)
date: 2017-08-02 11:44:12
tags: [Python, 自己写爬虫]
categories: [Python, 自己写爬虫]
---

# 前言

在 [Python爬虫爬取简书首页文章(一)](http://www.jensondev.me/2017/08/02/Python%E7%88%AC%E8%99%AB%E7%88%AC%E5%8F%96%E7%AE%80%E4%B9%A6%E9%A6%96%E9%A1%B5%E6%96%87%E7%AB%A0-%E4%B8%80/) 中已经完成了一个mini爬虫，完成了基本的爬取和存储工作。

但是还有很多问题需要亟需解决，譬如分页抓取就是本章要解决的。

要实现分页爬取，首先要知道网站如何分页的。

<!-- more -->

# 网站如何分页

这里以简书为例，要想知道简书首页文章如何实现分页，首先打开官网和抓包工具进行监听。

这里的抓包工具我使用的时**Wireshark**。刚开始时数据流是空的：

![空](http://othg5ggzi.bkt.clouddn.com/%E7%A9%BAWireshark.png)

现在开始滑动简书官网，直到页面底部出现**阅读更多**按钮，这滑动期间竟然也发生了两次网络请求：

![滑动期间的两次请求](http://othg5ggzi.bkt.clouddn.com/%E7%AE%80%E4%B9%A6%E9%A6%96%E9%A1%B5%E6%BB%91%E5%8A%A8%E4%B8%A4%E6%AC%A1%E8%AF%B7%E6%B1%82.png)

GET请求的url是这样子的：

/?seen_snote_ids%5B%5D=15296134&seen_snote_ids%5B%5D=15306523&seen_snote_ids%5B%5D=15298503&seen_snote_ids%5B%5D=15240154&seen_snote_ids%5B%5D=15274107&seen_snote_ids%5B%5D=15256726&seen_snote_ids%5B%5D=15290950&seen_snote_ids%5B%5D=15301177&seen_snote_ids%5B%5D=15275389&seen_snote_ids%5B%5D=15266115&seen_snote_ids%5B%5D=15293985&seen_snote_ids%5B%5D=15289824&seen_snote_ids%5B%5D=15276555&seen_snote_ids%5B%5D=15269012&seen_snote_ids%5B%5D=15268330&seen_snote_ids%5B%5D=15285079&seen_snote_ids%5B%5D=15292494&seen_snote_ids%5B%5D=15284369&seen_snote_ids%5B%5D=15299782&seen_snote_ids%5B%5D=15011765&seen_snote_ids%5B%5D=15304980&seen_snote_ids%5B%5D=15287206&seen_snote_ids%5B%5D=15273113&seen_snote_ids%5B%5D=15268608&seen_snote_ids%5B%5D=15220769&seen_snote_ids%5B%5D=15152802&seen_snote_ids%5B%5D=15280804&seen_snote_ids%5B%5D=15229091&seen_snote_ids%5B%5D=15288809&seen_snote_ids%5B%5D=15267285&seen_snote_ids%5B%5D=14630860&seen_snote_ids%5B%5D=15290958&seen_snote_ids%5B%5D=15262258&seen_snote_ids%5B%5D=15299674&seen_snote_ids%5B%5D=15294416&seen_snote_ids%5B%5D=15262595&seen_snote_ids%5B%5D=15271868&seen_snote_ids%5B%5D=15184645&seen_snote_ids%5B%5D=14818734&seen_snote_ids%5B%5D=15254172&page=3

当我点击**阅读更多**时候的请求url是这样子的：

/?page=4&seen_snote_ids%5B%5D=15296134&seen_snote_ids%5B%5D=15306523&seen_snote_ids%5B%5D=15298503&seen_snote_ids%5B%5D=15240154&seen_snote_ids%5B%5D=15274107&seen_snote_ids%5B%5D=15256726&seen_snote_ids%5B%5D=15290950&seen_snote_ids%5B%5D=15301177&seen_snote_ids%5B%5D=15275389&seen_snote_ids%5B%5D=15266115&seen_snote_ids%5B%5D=15293985&seen_snote_ids%5B%5D=15289824&seen_snote_ids%5B%5D=15276555&seen_snote_ids%5B%5D=15269012&seen_snote_ids%5B%5D=15268330&seen_snote_ids%5B%5D=15285079&seen_snote_ids%5B%5D=15292494&seen_snote_ids%5B%5D=15284369&seen_snote_ids%5B%5D=15299782&seen_snote_ids%5B%5D=15011765&seen_snote_ids%5B%5D=15304980&seen_snote_ids%5B%5D=15287206&seen_snote_ids%5B%5D=15273113&seen_snote_ids%5B%5D=15268608&seen_snote_ids%5B%5D=15220769&seen_snote_ids%5B%5D=15152802&seen_snote_ids%5B%5D=15280804&seen_snote_ids%5B%5D=15229091&seen_snote_ids%5B%5D=15288809&seen_snote_ids%5B%5D=15267285&seen_snote_ids%5B%5D=14630860&seen_snote_ids%5B%5D=15290958&seen_snote_ids%5B%5D=15262258&seen_snote_ids%5B%5D=15299674&seen_snote_ids%5B%5D=15294416&seen_snote_ids%5B%5D=15262595&seen_snote_ids%5B%5D=15271868&seen_snote_ids%5B%5D=15184645&seen_snote_ids%5B%5D=14818734&seen_snote_ids%5B%5D=15254172&seen_snote_ids%5B%5D=15272682&seen_snote_ids%5B%5D=15302506&seen_snote_ids%5B%5D=15238434&seen_snote_ids%5B%5D=15302851&seen_snote_ids%5B%5D=15256354&seen_snote_ids%5B%5D=15297896&seen_snote_ids%5B%5D=15246028&seen_snote_ids%5B%5D=15292615&seen_snote_ids%5B%5D=15215522&seen_snote_ids%5B%5D=15295152&seen_snote_ids%5B%5D=15246711&seen_snote_ids%5B%5D=15276820&seen_snote_ids%5B%5D=15274436&seen_snote_ids%5B%5D=15300621&seen_snote_ids%5B%5D=15296525&seen_snote_ids%5B%5D=15297151&seen_snote_ids%5B%5D=15298014&seen_snote_ids%5B%5D=11621895&seen_snote_ids%5B%5D=15268868&seen_snote_ids%5B%5D=15280076

通过这两个url可以看到每次都携带一个page参数，应该是表示请求页数了，

所以可以知道目前简书的分页是这样的：第一次打开时加载几条，在下滑过程中会产生两次请求，分别是page=2和page=3，然后再次滑动到底部时出现**阅读更多**按钮，后面的分页靠按钮点击实现。

综合来讲，简书的分页是下滑和点击共同实现的。

分析完了简书如何分页，下面分析爬虫如何分页抓取数据。

# 爬虫如何分页

一般来说爬虫分页有2种实现方式：①找到网址分页所须参数，通过发起网络请求对应的页数。②模拟人类对浏览器下滑、点击等操作实现分页。

对于简书官网，我最先使用了第一种方法，每次请求携带page和seen_snote_ids参数，但是仍然返回数据有问题。可能服务器还对其他首部信息做了校验。

看下请求信息：

     [truncated]GET /?seen_snote_ids%5B%5D=15296134&seen_snote_ids%5B%5D=15306523&seen_snote_ids%5B%5D=15298503&seen_snote_ids%5B%5D=15240154&seen_snote_ids%5B%5D=15274107&seen_snote_ids%5B%5D=15256726&seen_snote_ids%5B%5D=15290950&seen_snote_
    Host: www.jianshu.com\r\n
    Connection: keep-alive\r\n
    Accept: text/html, */*; q=0.01\r\n
    X-CSRF-Token: jrzSqMWq+Gqjm66lMiTJQQYSKEZKUYAYV9oOPghKMdJiJs9Oij1Q/bhsdugAnKKEjhsAokQqiog7MnW9ojznxQ==\r\n
    User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36\r\n
    X-Requested-With: XMLHttpRequest\r\n
    X-INFINITESCROLL: true\r\n
    Referer: http://www.jianshu.com/\r\n
    Accept-Encoding: gzip, deflate\r\n
    Accept-Language: zh-CN,zh;q=0.8,en;q=0.6\r\n
     [truncated]Cookie: signin_redirect=http%3A%2F%2Fwww.jianshu.com%2F; _ga=GA1.2.508983344.1500518688; _gid=GA1.2.101137453.1501499488; _gat=1; _session_id=NjJQQWdSSEl2cWdtSEc0bmRTQnZXQTVQenNDbTV1V0Z5M2N5OGpxbnhyell5Tnl5UHp6UHZJSncvVzNSeGlEU
    If-None-Match: W/"09cd78bd0d8a197d769d2c5b6b1e84e9"\r\n
    \r\n
    [Full request URI [truncated]: http://www.jianshu.com/?seen_snote_ids%5B%5D=15296134&seen_snote_ids%5B%5D=15306523&seen_snote_ids%5B%5D=15298503&seen_snote_ids%5B%5D=15240154&seen_snote_ids%5B%5D=15274107&seen_snote_ids%5B%5D=15256726&seen_]
    [HTTP request 1/2]
    [Next request in frame: 28]
由于请求信息太多，分析起来比较困难，所以决定采用方法②模拟行为。

要模拟人类下滑、点击浏览器行为需要使用自动化测试库**selenium**。



# Selenium使用

继续上篇文章的创建的项目，创建JSSpider.py文件

该文件中测试selenium库

我这里使用Chrome浏览器，需要安装ChromeDriver，具体方法见：[Selenium ChromeDriver安装](http://www.jensondev.me/2017/08/02/Selenium-ChromeDriver%E5%AE%89%E8%A3%85/)。

用selenium实现分页，可以分为三步：

①导入所需类：

```python
from selenium import webdriver
from selenium.webdriver.remote.webelement import WebElement
import time
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.support.wait import WebDriverWait as wait
from selenium.webdriver.common.by import By
```

②实现窗口下滑：

```python
browser = webdriver.Chrome(executable_path="/Users/jenson/Downloads/chromedriver")
browser.get("http://www.jianshu.com")
browser.maximize_window()
time.sleep(3)
browser.execute_script("window.scrollTo(0, document.body.scrollHeight);")
time.sleep(3)
browser.execute_script("window.scrollTo(0, document.body.scrollHeight);")
# 前三页都已经出来了
```

通过两次调用`browser.execute_script`，导致窗口两次自动下滑，会自动请求服务器。

③等待阅读更多按钮显示后模拟点击：

```python
try:
    loadMoreEle = wait(browser, 10).until(
        EC.presence_of_element_located((By.CLASS_NAME, "load-more"))
    )
except RuntimeError as e:
    print(e)
# loadMoreEle = browser.find_element_by_class_name("load-more")
assert isinstance(loadMoreEle, WebElement)
# time.sleep(5)
is_displayed = loadMoreEle.is_displayed()
print(is_displayed)
time.sleep(5)
if is_displayed:
    loadMoreEle.click()
```

插播一条消息：如果selenium启动Chrome浏览器，会有这样一条提示信息：

![selenium控制浏览器提示](http://othg5ggzi.bkt.clouddn.com/selenium%E6%8E%A7%E5%88%B6%E6%B5%8F%E8%A7%88%E5%99%A8%E6%8F%90%E7%A4%BA.png)

如果上面都已经实现且执行成功，那么可以开始把两个部分代码进行合并了。



# 实现分页

这里的实现分页只要把上一篇的代码和本节代码合并，基本就可以完成简书首页文章的抓取，下面看下具体实现：

## 首先实现下滑分页爬取部分



```python
from selenium import webdriver
from selenium.webdriver.remote.webelement import WebElement
import time
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.support.wait import WebDriverWait as wait
from selenium.webdriver.common.by import By
import main.JS as JS

url_str = "http://www.jianshu.com"
browser = webdriver.Chrome(executable_path="/Users/jenson/Downloads/chromedriver")
browser.get(url_str)
browser.maximize_window()
time.sleep(3)
browser.execute_script("window.scrollTo(0, document.body.scrollHeight);")
time.sleep(3)
browser.execute_script("window.scrollTo(0, document.body.scrollHeight);")
# 前三页都已经出来了,

# 获取前三页内容
time.sleep(3)
list = JS.getArticles(browser, False)
JS.saveCSV(list, True)
```

上一篇中写的`getArticles`方法添加了2个参数，**browser**和**isClick**，去掉了**url**参数。因为内容都已经在**browser**这个selenium启动的浏览器中了，所以不需要在使用**urllib**进行网络请求，也不再使用**bs4**来进行DOM解析，一切都交由**browser**完成。**isClick**为TRUE表示点击翻页，否则表示滑动翻页。

看下目前`getArticles`的实现

```python
def getArticles(browser, isClick=False):
    """
    根据url获取当前页面文章列表
    :param url:
    :param isClick:
    :return:
    """

    article_list = []
    assert isinstance(browser, webdriver.Chrome)
    ulEle = browser.find_element_by_class_name("note-list")
    assert isinstance(ulEle, WebElement)
    eles = ulEle.find_elements_by_xpath("//li[@id]")
    print(len(eles))
    if isClick:
        return iteratorEle(eles, article_list, len(eles) - 20, len(eles))
    else:
        return iteratorEle(eles, article_list, 0, len(eles))


def iteratorEle(eles, article_list, start, end):
    """
    :param eles:
    :param article_list:
    :param start:
    :param end:
    :return:
    """
    for index in range(start, end):
        item = eles[index]
        assert isinstance(item, WebElement)
        article = Article()
        article.author = item.find_element_by_class_name("blue-link").text
        titleEle = item.find_element_by_class_name("title")
        assert isinstance(titleEle, WebElement)
        article.title = titleEle.text
        article.url = titleEle.get_attribute("href")
        article_list.append(article)
    return article_list
```

另外保存csv文件的方法`saveCSV`也有改动，添加了一个**setTag**参数。为True表示设置首行的标记。方法实现如下：

```python
def saveCSV(articles, setTag=False):
    """
    保存到csv文件
    :param articles:
    :return:
    """
    csvFile = open("/Users/jenson/Downloads/js.csv", 'a')
    csv_writer = csv.writer(csvFile)
    # print(type(csv_writer))
    # assert isinstance(csv_writer, csv.DictWriter)
    if setTag:
        csv_writer.writerow(["作者", "标题", "链接"])
    for article in articles:
        csv_writer.writerow(
            [article.author, article.title, article.url])
```



## 其次实现点击分页爬取部分

模拟点击翻页中使用while循环检测**阅读更多**按钮是否可见，如果可见则每次模拟点击实现翻页，代码实现如下：

```python
# 模拟点击翻页
try:
    loadMoreEle = wait(browser, 10).until(
        EC.presence_of_element_located((By.CLASS_NAME, "load-more"))
    )
except RuntimeError as e:
    print(e)
# loadMoreEle = browser.find_element_by_class_name("load-more")
assert isinstance(loadMoreEle, WebElement)
# time.sleep(5)
try:
    while loadMoreEle.is_displayed():
        loadMoreEle.click()
        time.sleep(3)
        list = JS.getArticles(browser, True)
        JS.saveCSV(list)
except RuntimeError as err:
    print(err)

```



经过以上步骤，翻页爬取简书首页文章就已经完成了，最终爬取结果如下：

![简书首页文章爬取到末尾](http://othg5ggzi.bkt.clouddn.com/%E7%AE%80%E4%B9%A6%E9%A6%96%E9%A1%B5%E6%9C%AB%E5%B0%BE%E6%96%87%E7%AB%A0%E7%88%AC%E5%8F%96%E7%BB%93%E6%9D%9F.png)

可以看到文章末尾已经没有了**阅读更多**字样，说明已经翻到底儿了。而CSV文件中内容最后一条也刚好符合简书网站最后一条。可见抓取成功。





> 注意：一旦使用了selenium，后面的DOM解析都要使用对应的浏览器Driver进行解析，如果还是使用bs4是获取不到新内容的。
>
> 