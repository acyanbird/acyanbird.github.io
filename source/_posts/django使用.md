---
title: django使用
date: 2022-01-19 20:16:29
categories:
tags:
---

一边写 docker 一边写 django 吧，欠债还钱啊。首先对照官方文档，它们已经不那么 shit 了

https://docs.djangoproject.com/zh-hans/4.0/intro/tutorial01/

参照这个，manage.py 上面的那一层目录可以随意重命名

叮咚，__init__.py 的妙用

https://www.jianshu.com/p/73f7fbf75183

方便导入多个的时候执行



app 负责具体功能实现，project 本身装载，然而最高层的 url.py 才是标记哪里到哪里的，视图大概是会体现什么？



函数 [`include()`](https://docs.djangoproject.com/zh-hans/4.0/ref/urls/#django.urls.include) 允许引用其它 URLconfs。每当 Django 遇到 [`include()`](https://docs.djangoproject.com/zh-hans/4.0/ref/urls/#django.urls.include) 时，它会截断与此项匹配的 URL 的部分，并将剩余的字符串发送到 URLconf 以供进一步处理。



所以只要包含了就行？

```
path('polls/', include('polls.urls')),
```

[`path()`](https://docs.djangoproject.com/zh-hans/4.0/ref/urls/#django.urls.path) 参数： `route`[¶](https://docs.djangoproject.com/zh-hans/4.0/intro/tutorial01/#path-argument-route)

`route` 是一个匹配 URL 的准则（类似正则表达式）。当 Django 响应一个请求时，它会从 `urlpatterns` 的第一项开始，按顺序依次匹配列表中的项，直到找到匹配的项。

这些准则不会匹配 GET 和 POST 参数或域名。例如，URLconf 在处理请求 `https://www.example.com/myapp/` 时，它会尝试匹配 `myapp/` 。处理请求 `https://www.example.com/myapp/?page=3` 时，也只会尝试匹配 `myapp/`。

[`path()`](https://docs.djangoproject.com/zh-hans/4.0/ref/urls/#django.urls.path) 参数： `view`[¶](https://docs.djangoproject.com/zh-hans/4.0/intro/tutorial01/#path-argument-view)

当 Django 找到了一个匹配的准则，就会调用这个特定的视图函数，并传入一个 [`HttpRequest`](https://docs.djangoproject.com/zh-hans/4.0/ref/request-response/#django.http.HttpRequest) 对象作为第一个参数，被“捕获”的参数以关键字参数的形式传入。稍后，我们会给出一个例子



