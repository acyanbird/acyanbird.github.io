---
title: intellij 远程开发
date: 2021-11-27 00:07:21
categories:
tags:
---

写数据库就需要直接连接到那里去了

http://quanzhan.applemei.com/webStack/TlRVeE53PT0=

不知道为什么没法截图……只能这样了，在工具栏里面选择 Tools -> deployment -> browse remote host  三个小点选择添加 SFTP，添加 SSH configuration，连接上去之后，服务器的 root 目录填写一个你觉得方便的。web 那个不知道是怎么回事……

然后在 mapping 那里设置，其中本地要写 project 的绝对路径，服务器那边写你填写的 root 的相对路径。然后在 deployment option 可以选择always syn 或者 ctrl s 才 syn，always 有点卡，所以 ctrl s 就好了

