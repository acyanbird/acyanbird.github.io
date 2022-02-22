---
title: postgresql与django配合
date: 2022-01-21 01:00:14
categories:
tags:
---

呜呜呜要配合那个 OS 环境在本地得好好配置了，不过本来就是要的嘛qwq

https://pythondjango.cn/django/basics/12-databases/#%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8postgresql%E6%95%B0%E6%8D%AE%E5%BA%93

先看一下它有没有启动

`systemctl status postgresql`

没有就 start 一下要停就 stop，这个看具体设定了嗯

然后进入命令行`sudo -u postgres psql`

```
# 创建名为myapp的数据库
CREATE DATABASE mydb; 
# 创建用户名和密码
CREATE USER myuser WITH  PASSWORD 'mypass'; 
# 给创建的用户授权
GRANT ALL PRIVILEGES ON DATABASE mydb TO myuser;

# 以下设置可手动进行设置，也可以在postgresql.conf中进行配置
# 设置客户端字符为utf-8，防止乱码
ALTER ROLE myuser SET client_encoding TO 'utf8';
```

然后看看这个 setting

```
DATABASES = {
    'default': {
        # Always force to use PostgreSQL for now
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': os.environ.get('DB_NAME'),
        'USER': os.environ.get('DB_USER'),
        'PASSWORD': os.environ.get('DB_PASSWORD'),
        'HOST': os.environ.get('DB_HOST'),
        'PORT': int(os.environ.get('DB_PORT')),
    }
}
```

```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql_psycopg2',   # 数据库引擎
        'NAME': 'mydb',         # 数据库名，Django不会帮你创建，需要自己进入数据库创建。
        'USER': 'myuser',     # 设置的数据库用户名
        'PASSWORD': 'mypass',     # 设置的密码
        'HOST': 'localhost',    # 本地主机或数据库服务器的ip
        'PORT': '',         # 数据库使用的端口
    }
```

我去我去，因为 db 是不分大小写的，所以在创建的时候使用的大写都会变成小写，在 django 里写大写就会报错！！！！！！！

一定要全部小写啊！！！！！！！！

啊不对，如果你密码用的是大写也必须大写，人家应该是看你 md5 的！还有不要 encrypt，with password 就好了



如何连接到psql，先转到 postgres `sudo su postgres` 然后连接哈