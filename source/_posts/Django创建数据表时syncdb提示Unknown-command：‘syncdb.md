---
title: Django创建数据表时syncdb提示Unknown command：‘syncdb'
date: 2017-06-27 16:32:23
tags: Python
categories: Python
---

异常信息如下

```
JensondeMini:dj_test01 jenson$ python3 ./manage.py syncdb
Unknown command: 'syncdb'
Type 'manage.py help' for usage.
```

主要是因为使用的版本太新，如果你安装的Django Version >= 1.9就会出现这个问题

解决方法就是把`python3 ./manage.py syncdb`

替换成`python3 ./manage.py migrate`

之后就会初始化数据表成功

```
JensondeMini:dj_test01 jenson$ python3 ./manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying auth.0008_alter_user_username_max_length... OK
  Applying sessions.0001_initial... OK
```





