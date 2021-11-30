---
title: fpga game
date: 2021-11-15 15:48:33
categories: 学校学习
tags:
---

展示 5 到 10 min，使用学校电脑，show 所有的features

info bar 可 change

顺便放一些碰到的稀奇古怪问题……哦那个 vga_out 不完整，现在尝试补全ing



740 display 宽度，柱子宽度是 180，柱子之间间隔 190，上下间隔

注意！最顶层的 module 才和板子交互，不是 vga_out 和板子交互！所以要输出的端口需要在最顶层定义！

=======
问题出现，不可以使用 assign 去传递 draw 和 draw_r，但是为什么 curr_x 可以？

assign draw_r = draw_r_r;
assign draw_g = draw_g_r;
assign draw_b = draw_b_r;

这样不可以，会无法显示



一定要记住如果 output 是 reg那就 declare reg！



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



当 output 是 reg 的时候必须声明，可以在 moudle declare 里面

```verilog
module pad(
    input        DIN, OEN ,
    input [1:0]  PULL ,
    inout        PAD ,
    output reg   DOUT
    );
```

这样才行



数组定义

定义是大位在前面，但是赋值的时候还是从后面开始计算的

![](https://i.imgur.com/zFLo3rr.png)

![](https://i.imgur.com/MJN9B0I.png)

所以多个位的时候要从大到小，但是单个定位是右边是 0 这样



记住 input 和 output 要定义这个range啊！



注意 decalre instance 的时候 instance 名称是否正确……



模块例化一定要定义 wire 去承接，即使只有这两个模块使用 top 完全不使用，也必须 declare wire。



如果出现问题：

检查端口是否宽度正确

检查是否标注 reg

检查变量名是否填写正确

检查是否有例化模块输入S

/home/cyanbird/study/warwick/year2/ES2E3DigitalSystem/lab/lab5/lab5.srcs/sources_1/new/ground.v



只有 135 个块可以放图片……省着点来吧。一个 ghost 是 3，info 是 48

可以在双色图片里面导入黑白，然后根据 1 和 0 赋值颜色，大大大省空间！



https://stackoverflow.com/questions/34011576/generating-random-numbers-in-verilog

生成随机数，生成柱子



IP 生成的名字不可以和现有文件一致，不然会出现包含错误