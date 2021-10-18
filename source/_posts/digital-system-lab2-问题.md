---
title: digital system lab2 问题
date: 2021-10-18 10:19:31
categories: 学校学习
tags:
---

##### 为什么不能在两个 always 块中赋值同一个变量？

verilog 本身不允许两个 always 块赋值同一个变量，如果你这么写了，它会将两个块用自己的方式组合起来，这会造成不可预知的错误，所以尽量避免这种写法。



##### always 块的作用？

always语句块从仿真0时刻开始执行其中的行为语句；最后一条执行完成后，再开始执行其中的第一条语句，如此往复循环，直到整个仿真结束。所以它叫 always 嘛



##### always@(*)和always不加@的区别

1. 若没有@，则是一般在teastbench 中产生时钟信号，指不会满足特定的条件，执行完一次后立马继续执行下一次，一直重复执行。 
2.  有@时，是每次执行语句时，必须满足括号内的条件才能继续执行语句，否则不执行。

所以 always 可以直接像 wire 一样在单个语句之前赋值。



##### always 块的触发机制

always@(a)

a信号发生变化是触发
always@（posedge a or negedge a）

a信号双边沿触发
always

不断触发，伪组合逻辑电路
always@（*）

任意输出信号发生变化触发
always@（a or posedge clk）

clk上升沿或a发生变化触发
always@（data[2:0]）

data是8位数据，前三位data[2:0]发生变化触发

ref：

https://blog.csdn.net/gududeyhc/article/details/8795202

https://blog.csdn.net/qq_33929689/article/details/51842606

https://www.cxyzjd.com/article/vagrantda/79711134

https://blog.csdn.net/qq_45467083/article/details/103368097

