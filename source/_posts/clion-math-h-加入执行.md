---
title: clion math.h 加入执行
date: 2021-10-18 21:14:12
categories: 学校学习
tags:
---

```c
target_link_libraries(<文件名称> m)
```

m 是 math 的缩写，似乎是因为 math 算是 seperate lib 所以要这么连接？并不知道具体 cmake 是怎么操作的， add executable 也是，未解之谜了属于是（

挖坑！一定填上！

这个方法似乎不适用于用 single execution 搞定的……我得更深研究 cmake 才行，现在先那啥，用 gcc 吧。
