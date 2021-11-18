---
title: fpga game
date: 2021-11-15 15:48:33
categories: 学校学习
tags:
---

展示 5 到 10 min，使用学校电脑，show 所有的features

info bar 可 change



问题出现，不可以使用 assign 去传递 draw 和 draw_r，但是为什么 curr_x 可以？

assign draw_r = draw_r_r;
assign draw_g = draw_g_r;
assign draw_b = draw_b_r;

这样不可以，会无法显示



![](https://img-blog.csdnimg.cn/20190929201409920.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwMTgxNTky,size_16,color_FFFFFF,t_70)

可能是 input output 端口问题？外界输入进取的可以是reg 或者 wire，接收方需要是 wire，内部也是 output 可以是 reg 或者 wire，接收 input 是 wire



输入端口：从模块内部来讲，输入端口必须为线网数据类型，从模块外部来看，输入端口可以连接到线网或者reg数据类型的变量。

输出端口：从模块内部来讲，输出端口可以是线网或者reg数据类型，从模块外部来看，输出必须连接到线网类型的变量，而不能连接到reg类型的变量。

输入/输出端口

​    从模块内部来讲，输入/输出端口必须为线网数据类型；从

​    模块外部来看，输入/输出端口也必须连接到线网类型的变

​    量。



那么 vga_out 就可以用 reg 的 output 和 game_top 的 reg input。



好吧这个 xilinx 的 init 速度不快的，所以……要等它 update 完毕之后再跑，不然