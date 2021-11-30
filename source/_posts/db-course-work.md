---
title: db course work
date: 2021-11-14 17:51:07
categories: 学校学习
tags:
- sql
- java
---

https://www.postgresqltutorial.com/

嗯，本来应该在本机上安装sql的……不过还是不要搞这种操作了，先搞定 maven 的依赖。已经安装好了，所以直接 open pom.xml 就可以，intelliJ 会自己搞定的

刚开始 symbol java cannot solve，打开 File Menu -> Project Structure，将 JDK 改成 11

![](http://sbytestream.pythonanywhere.com/static/blog/How-to-fix-IntelliJ-cannot-resolve-symbol/intellij-project-settings.png)



一些命令

`mvn -q exec:java@gig`

quiet mode，only produce error

exec:java 应该是compile的意思，然后后面是项目名字？大概吧



划掉，现在进行远程开发，参见 intellj 远程调试

先是要创建 database，参考 lab 3 提供的创建方式，一个奇怪的东西是 serial，lab 3 有详细讲解，会自动从 1 开始排序，但是 del 全部数据之后不会重新开始排序，因为 del 并没有影响值。



![](https://i.imgur.com/EcQJIyZ.png)



各种 join 的区别

https://www.runoob.com/sql/sql-join.html



o make sure our data always starts from 1 lets set the value of the sequence to 1 with:

```
ALTER SEQUENCE observations_observationid_seq RESTART WITH 1;
```

we can add this to our `deleteAllData` method like this (uncomment these lines from the Java file):

```
delStatement = conn.prepareStatement("ALTER SEQUENCE observations_observationid_seq RESTART WITH 1;");
```

默认的 serial 是下一个值

 If data is inserted manually through SQL (via tests), and already has  an ID, you should use the one provided rather than generating a new one.

插入的需要使用它提供的值

不会输入 null 值，不过还是 judge 一下比较好

#### creating schema

调整格式，右键 change dialect to postgres sql，应用格式

Right-click any area in the editor and select Reformat. Alternatively, press Ctrl+Alt+L

serial 是 integer，需要

```sql
actID    INTEGER        NOT NULL REFERENCES act (actID),
```

绑定，不能使用 numeric

外键只可以存在 primary key 或者 unique key 中

主表必须已经存在于数据库中，或者是当前正在创建的表。

到时候应该把有外键的表往下挪

ticket 的 primary 是 email

NUMERIC（p,s）一共 p 位，其中 s 位是 小数点之后，5，2 表示 3 位前 2 位后。

出场费 10，3 人员 3，0



需要的约束

- [x] Each act charges an actfee
- [ ]  **ACT**: They have a particular time when they start (which must be
  after the start of the gig), and their performance lasts for a particular duration (a number of
  minutes). No gigs should go beyond 11:59pm. 加上持续时间，不超过 23:59
- [ ] Venues have a name, a hirecost,capacity
- [ ] ticket: email 和 name 是绑定的……用户永远用一个 email。



#### TODO

使用 SQL 方法维持 database？



#### TESTING

我不太懂所以还是自己测试吧……

```
CREATE DATABASE cwk
\c cwk
\i schema.sql
```

