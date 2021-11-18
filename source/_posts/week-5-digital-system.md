---
title: week 5 digital system
date: 2021-11-15 16:06:11
categories: 学校学习
tags:
- verilog
---

counter

![](https://i.imgur.com/qcuVMam.png)

非阻塞赋值的 shift register 实现

![](https://i.imgur.com/GlRJ4Pb.png)

不希望综合成 latch，所以要在每个 always 块的时候赋予新的值，不然 reg 可能变成 latch 导致错误

 ### Memory

https://www.runoob.com/w3cnote/verilog-data-type.html

memory 使用的是数组

在 Verilog 中允许声明 reg, wire, integer, time, real 及其向量类型的数组。

数组维数没有限制。线网数组也可以用于连接实例模块的端口。数组中的每个元素都可以作为一个标量或者向量，以同样的方式来使用，形如：<数组名>[<下标>]。对于多维数组来讲，用户需要说明其每一维的索引。例如：

```verilog
integer          flag [7:0] ; //8个整数组成的数组
reg  [3:0]       counter [3:0] ; //由4个4bit计数器组成的数组
wire [7:0]       addr_bus [3:0] ; //由4个8bit wire型变量组成的数组
wire             data_bit[7:0][5:0] ; //声明1bit wire型变量的二维数组
reg [31:0]       data_4d[11:0][3:0][3:0][255:0] ; //声明4维的32bit数据变量数组

```

下面显示了对数组元素的赋值操作：

```verilog
flag [1]   = 32'd0 ; //将flag数组中第二个元素赋值为32bit的0值
counter[3] = 4'hF ;  //将数组counter中第4个元素的值赋值为4bit 十六进制数F，等效于counter[3][3:0] = 4'hF，即可省略宽度;
assign addr_bus[0]        = 8'b0 ; //将数组addr_bus中第一个元素的值赋值为0
assign data_bit[0][1]     = 1'b1;  //将数组data_bit的第1行第2列的元素赋值为1，这里不能省略第二个访问标号，即 assign data_bit[0] = 1'b1; 是非法的。
data_4d[0][0][0][0][15:0] = 15'd3 ;  //将数组data_4d中标号为[0][0][0][0]的寄存器单元的15~0bit赋值为3
```

memory 就是 reg 的数组

`reg [15:0] ram [0:63];` 宽度 16，深度 64

```verilog
module spram (input clk, en, we,
              input [5:0] addr,
              input [15:0] di,
              output reg [15:0] do);
reg [15:0] ram [0:63];
always @(posedge clk) begin
    if (en) begin
        if (we)
            ram[addr]<=di;
        do <= ram[addr];
    end
end
endmodule
```

 ### FSM 

finite state machine，只拥有有限的 state

比如 increment counter 就是 FSM

![](https://i.imgur.com/8IrLOV1.png)

We can indicate conditions for transition on the transition arrows

