---
title: linux user mask 是什么
date: 2021-10-28 20:56:28
categories: 折腾
tags:
- linux
---

嗯……真的是，一直困扰我的问题吧，就是三位权限中的第一位，比如0777中的第一个就是 user mask，所以这个是什么呢

首先三位权限是 UGO 排布，user group other

标注：目录需要执行权限来打开

`drwxr-xr-x  2 niteshb users    4096 Mar 21 22:43 testdir`

第一个 d 就代表目录，它有 x 权限所以可以被打开

现在 terminal 输入 `$umask`得到

`0002`

然后我创建一个文件

`-rw-rw-r-- 1 lucia lucia 0 Oct 28 21:01 1`

前面是 group 后面是用户名，linux 默认创造文件的时候给 666，创造目录的时候给 777，所以现在就是最后一个 other 被减去了 2，只剩下 read 权限了。

设置 umask 则是

`umask 0022`

唉，那为什么要四位数呢……请看 777 vs 0777 这一篇~

ref:

https://www.howtoforge.com/linux-umask/
