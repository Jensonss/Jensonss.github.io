---
title: Python用qq邮箱发邮件
date: 2017-08-09 17:30:48
tags: Python
categories: Python
---

# 前言

如果经常无人看管的项目出现了异常，那比较好的办法就是发个邮件给运营人一个提示，这样方便及时处理。

所以写一个自动发邮件的模块就显得有必要了。

但是默认QQ邮箱是没有开启第三方收发支持的，所以我们首先要开启支持。

<!-- more -->

# 开启QQ邮箱支持

打开 QQ邮箱页面—>设置—>账户—>POP3/IMAP/SMTP/Exchange/CardDAV/CalDAV服务

开启需要的服务，我这里打开**POP3/SMTP服务**

![qq邮箱三方服务器设置](http://othg5ggzi.bkt.clouddn.com/qq%E9%82%AE%E7%AE%B1%E8%AE%BE%E7%BD%AE%E4%B8%89%E6%96%B9%E6%9C%8D%E5%8A%A1%E5%99%A8.png)



使用SSL的通用配置如下：

**接收邮件服务器：**pop.qq.com，使用SSL，端口号995

**发送邮件服务器：**smtp.qq.com，使用SSL，端口号465或587

**账户名：**您的QQ邮箱账户名（如果您是VIP帐号或Foxmail帐号，账户名需要填写完整的邮件地址）

**密码：**您的QQ邮箱密码

**电子邮件地址：**您的QQ邮箱的完整邮件地址

第三方需要使用授权码作为上面的密码。

# 用qq邮箱发送邮件

发送邮件使用**smtplib库**。

## 发送文本内容

```python
import smtplib
from email.mime.text import MIMEText
from email.header import Header

host = 'smtp.qq.com'  # 发送服务器地址
user = '840418528@qq.com'  # 验证用户名
pwd = '***'  # 验证密码(授权码)
sender = '840418528@qq.com'  # 发件人地址，和验证用户名应一致
receivers = ['840418528@qq.com']  # 接收邮件，可设置为你的QQ邮箱或者其他邮箱

message = MIMEText('a test for python', 'plain', 'utf-8')  # 邮件内容
message['From'] = Header("Jenson", 'utf-8')  # 发件人名字
message['To'] = Header("songjl", 'utf-8')  # 收件人名字
subject = '周报'  # 邮件标题
message['Subject'] = Header(subject, 'utf-8')

try:
    smtpObj = smtplib.SMTP_SSL(host, 465)
    login_result = smtpObj.login(user, pwd)
    if 235 == login_result[0]:  # 235表示登陆成功
        send_result = smtpObj.sendmail(sender, receivers, message.as_string())
        print("邮件发送成功")
    smtpObj.quit()

except smtplib.SMTPException as e:
    print(e)

```

上面我是自己给自己发了封邮件，打印显示成功：

![qq邮箱发送成功](http://othg5ggzi.bkt.clouddn.com/qq%E9%82%AE%E7%AE%B1%E5%8F%91%E9%80%81%E6%88%90%E5%8A%9F%E6%88%AA%E5%9B%BE.png)



接下来查看QQ邮箱：

![收到邮件](http://othg5ggzi.bkt.clouddn.com/%E6%94%B6%E5%88%B0%E9%82%AE%E4%BB%B6%E6%88%AA%E5%9B%BE.png)

上面虽然发送成功了，但是内容只是文本，下面试试如何发送HTML格式



## 发送HTML内容

```python
message = MIMEText(r'<p>发送测试</p><a href="www.jensondev.me">我的技术博客</a>', 'html', 'utf-8')  # 邮件内容

```

发送HTML内容，除了内容是HTML网页格式，还要把内容格式改为**‘HTML’**。

收到邮件内容如下：

![html邮件内容](http://othg5ggzi.bkt.clouddn.com/HTML%E5%86%85%E5%AE%B9%E9%82%AE%E4%BB%B6.png)

可以看到已经显示出链接来了。



## 发送附件内容

发送附件使用**MIMEMultipart**类

```python
import smtplib
from email.mime.text import MIMEText
from email.header import Header
from email.mime.multipart import MIMEMultipart

host = 'smtp.qq.com'  # 发送服务器地址
user = '840418528@qq.com'  # 验证用户名
pwd = '***'  # 验证密码(授权码)
sender = '840418528@qq.com'  # 发件人地址，和验证用户名应一致
receivers = ['840418528@qq.com']  # 接收邮件，可设置为你的QQ邮箱或者其他邮箱

# message = MIMEText(r'<p>发送测试</p><a href="www.jensondev.me">我的技术博客</a>', 'html', 'utf-8')  # 邮件内容
message = MIMEMultipart()
# 邮件正文内容
message.attach(MIMEText('这是正文内容', 'plain', 'utf-8'))

# 附件1，传送当前目录下的 txt 文件
att1 = MIMEText(open('test.txt', 'rb').read(), 'base64', 'utf-8')
att1["Content-Type"] = 'application/octet-stream'
# 这里的filename可以任意写，写什么名字，邮件中显示什么名字
att1["Content-Disposition"] = 'attachment; filename="test.txt"'
message.attach(att1)

# 构造附件2，传送当前目录下的 图片 文件
att2 = MIMEText(open('email.png', 'rb').read(), 'base64', 'utf-8')
att2["Content-Type"] = 'application/png'
att2["Content-Disposition"] = 'attachment; filename="email.png"'
message.attach(att2)

message['From'] = Header("Jenson", 'utf-8')  # 发件人名字
message['To'] = Header("songjl", 'utf-8')  # 收件人名字
subject = '带附件周报'  # 邮件标题
message['Subject'] = Header(subject, 'utf-8')

try:
    smtpObj = smtplib.SMTP_SSL(host, 465)
    login_result = smtpObj.login(user, pwd)
    if 235 == login_result[0]:  # 235表示登陆成功
        send_result = smtpObj.sendmail(sender, receivers, message.as_string())
        print("邮件发送成功")
    smtpObj.quit()

except smtplib.SMTPException as e:
    print(e)


```



收到内容如下：

![带邮件附件](http://othg5ggzi.bkt.clouddn.com/%E5%B8%A6%E9%99%84%E4%BB%B6%E9%82%AE%E4%BB%B6.png)

可见文本和图片都已经收到了。

## 附件中文乱码问题

```python
att2["Content-Disposition"] = 'attachment; filename= '+filename

```

这里的filename只能是字符串，但是如果encode('utf-8')后是字节，这样行不通。

但是**Content-Disposition**这个属性可以通过另一种方式添加：

```python
att2.add_header('Content-Disposition', 'attachment',filename=('gbk','',filename))

```

这时filename就可以设置为元组了，可以同时传编码和文件名称。

可以参考[官方文档](https://docs.python.org/3/library/email.compat32-message.html#email.message.Message.add_header)。



