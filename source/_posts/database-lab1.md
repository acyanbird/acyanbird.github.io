---
title: database lab1
date: 2021-10-19 16:08:17
categories: 学校学习
tags: 
- sql
- dblab
---

##### 安装并使用 PostgreSQL

```bash
sudo apt install postgresql postgresql-contrib
sudo -i -u postgres
psql
```

要切换用户到 postgres 才行，真是很奇怪耶……



好吧因为数据库还是有区别所以先放弃本机使用~ssh 吧

因为没有办法添加 path 所以每一次都需要

```bash
/modules/cs258/bin/psql <database name>#
但是只能用 postgres，其他的不行，为什么？
```

然后要看看服务器是否打开……

```
/modules/cs258/bin/postgres-server [start|stop] 
// 没有 status 请注意
```

然后可以 create database

使用 `\c nature;` 连接到指定数据库。

ref:

https://www.digitalocean.com/community/tutorials/how-to-install-postgresql-on-ubuntu-20-04-quickstart
