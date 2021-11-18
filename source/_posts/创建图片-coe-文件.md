---
title: 创建图片 coe 文件
date: 2021-11-16 22:59:48
categories: 学校学习
tags:
---

记笔记记笔记，不然之后会死球的

 COE 文件是 xilinx 家的文件格式，； 打头的是注释，一定要填写的参数是

```
memory_initialization_radix=16
这个是显示用几进制，这里选用 16 进制所以是 16，2 进制就是 2
memory_initialization_vector=
100,
100,
...
100;
放数据，每一个用逗号隔开，分号在最后结束
```



算了接下来就在这里写吧

https://blog.csdn.net/qq_39507748/article/details/109226052

先在 IP 核里选择ROM

![](https://img-blog.csdnimg.cn/20201022170521320.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NTA3NzQ4,size_16,color_FFFFFF,t_70#pic_center)

选择单口 ROM

![](https://img-blog.csdnimg.cn/20201022170528359.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NTA3NzQ4,size_16,color_FFFFFF,t_70#pic_center)

选择宽度/深度，因为是 3 个 16 进制的数字，所以每个 4 bits，一共 12 bits。深度就是有多少数字需要被读取，是 image size，这里是100*100

![](https://img-blog.csdnimg.cn/2020102217053586.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NTA3NzQ4,size_16,color_FFFFFF,t_70#pic_center)

然后用内部文件初始化

![](https://img-blog.csdnimg.cn/20201022170541831.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NTA3NzQ4,size_16,color_FFFFFF,t_70#pic_center)

之后简单读取按照时间递增的地址，IP 的实例化可以在 .veo 里找到

![](https://i.imgur.com/JqgCMlE.png)





外部连接input端口的可以是wire也可以是reg

外部连接output端口的必须是wire

外部连接inout端口的必须是wire



总而言之全部是 wire 应该就可以了吧
