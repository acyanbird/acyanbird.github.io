---
title: db course work
date: 2021-11-14 17:51:07
categories: 学校学习
tags:
- sql
- java
---

嗯，本来应该在本机上安装sql的……不过还是不要搞这种操作了，先搞定 maven 的依赖。已经安装好了，所以直接 open pom.xml 就可以，intelliJ 会自己搞定的

刚开始 symbol java cannot solve，打开 File Menu -> Project Structure，将 JDK 改成 11

![](http://sbytestream.pythonanywhere.com/static/blog/How-to-fix-IntelliJ-cannot-resolve-symbol/intellij-project-settings.png)



一些命令

`mvn -q exec:java@gig`

quiet mode，only produce error

exec:java 应该是compile的意思，然后后面是项目名字？大概吧
