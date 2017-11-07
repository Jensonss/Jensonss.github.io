代码同时同步到码云、github平台



git允许我们设置多个remote接收push。

`git remote add`添加一个，然后使用`git remote set-url --add`命令添加后续的url。

然后通过`git config --local  —list`命令查看配置的remote是否为多个。



另外码云的项目管理中有个添加远程同步，这个功能是把其他平台代码同步到本地并覆盖。

所以如果单纯是把github的项目同步到码云，这种单向操作也可以使用此功能。













Django运行访问项目出现的问题:DisallowedHost at / Invalid HTTP_HOST header:

# 异常信息

```
Invalid HTTP_HOST header: 'jenson.pythonanywhere.com/'. You may need to add u'jenson.pythonanywhere.com/' to ALLOWED_HOSTS.
```

# 解决

打开项目的setting.py 文件：

在**ALLOWED_HOSTS = []**中添加*  

ALLOWED_HOSTS = [‘*’]这样，reload后即可访问主页面