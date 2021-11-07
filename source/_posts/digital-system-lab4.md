---
title: digital system lab4
date: 2021-11-01 14:07:08
categories: 学校学习
tags:
- verilog
- digitalLab
---

##### 阻塞赋值和非阻塞赋值看这里！

https://zhuanlan.zhihu.com/p/72034401

***阻塞赋值的执行可以认为是只有一个步骤的操作：***

 计算RHS 并更新LHS，此时不能允许有来自任何其他Verilog 语句的干扰。 所谓阻塞的概念是指在同一个always 块中，其后面的赋值语句从概念上（即使不设定延迟）是在前一句赋值语句结束后再开始赋值的。

***非阻塞赋值的操作可以看作为两个步骤的过程：***

1) 在赋值时刻开始时，计算非阻塞赋值RHS 表达式。

2) 在赋值时刻结束时，更新非阻塞赋值LHS 表达式。

是相反的呀~

##### 在一个 always 里两次赋值同一个变量

叮咚，那是不可以的！如果需要有一个这个 reach some point and turn to other value 的事件，请使用别的变量唷



还是使用啃源码方法罢（苦笑

```verilog
`timescale 1ns / 1ps

module vga_out(
    input clk,
    input [2:0] sw,
    output [3:0] pix_r,
    output [3:0] pix_g,
    output [3:0] pix_b,
    output hsync,
    output vsync
    );
    
    // input 3 个按钮代表rgb，换算成 output,还有hsync和vsync表示是否需要输出
    wire pixclk;
    
    clk_wiz_0 instance_name(.clk_out1(pixclk),.clk_in1(clk));
    
    reg [10:0] hcount = 0;
    reg [9:0] vcount = 0;
    
    wire line_end = (hcount == 11'd1903);
    wire frame_end = (vcount == 10'd931);

    assign display_region = ((hcount >= 11'd384) && (hcount <= 11'd1823) && (vcount >= 10'd31) && (vcount <= 10'd930));
    
    // why? resolution is 640x480
    // is 1440 * 900
    assign hsync = ((hcount >= 11'd0) && (hcount <= 11'd151));
    assign vsync = ((vcount >= 10'd0) && (vcount <= 10'd2));
        
    // condition ? if true : if false
    assign pix_r = (display_region && sw[0]) ? 4'b0100 : 4'b0000;
    assign pix_g = (display_region && sw[1]) ? 4'b0100 : 4'b0000;
    assign pix_b = (display_region && sw[2]) ? 4'b0100 : 4'b0000;
      
    always@(posedge pixclk) begin
        if(line_end) begin
            hcount <= 11'd0;
        end else begin
            hcount <= hcount + 1;
        end
    end
    
    always@(posedge pixclk) begin
        if(frame_end) begin
            vcount <= 10'd0;
        end else begin
            if(line_end) begin
                vcount <= vcount + 1;
            end
        end
    end
    
endmodule
```



1. `timescale 1ns / 1ps，含义为：时延单位为1ns，时延精度为1ps。

2. 在编译过程中，`timescale会影响其后面所有模块中的时延值，直至遇到另一个

完全不明白……之后大概会懂吧

为了让里面的 wizard 活动，

`clk_wiz_0 instance_name(.clk_out1(pixclk),.clk_in1(clk));`

使用左边的 IP Catalog 去 import clocking wizard, 在 customize 里面将 request hz 改成要求的 104.47，取消 locked 和 reset 功能（在同一页的最下面），



**这里的分辨率是 1440 * 900 啊！不是 VGA 标准的那个啊！你驴我！**

整个时钟是 106.47 MHz, 算上 VS 和 HS 是 （1903*931) 个像素一frame，除过来约等于 60，所以刷新率大概是 60





#### Testbench

![testbench](/home/cyanbird/cs/blog/web/source/images/testbench.png)



其实基本就是你模拟输入，查看输出是否是你想要的

```verilog
`timescale 1ns / 1ps
module vga_test();

reg clk;
reg [3:0] sw;

reg [3:0] pix_r, pix_g, pix_b;
wire hsync, vsync;

initial 
begin
    clk = 0;
    sw = 4'b1111;
end
    // 在仿真开始是初始化变量

always
begin
    #1 clk = ~clk;
    // one unit of delay,1 unit 之后反向？delay unit 是什么呢
end

vga inst_tb(
    .clk(clk),
    .sw(sw),
    .pix_r(pix_r),
    .pix_g(pix_g),
    .pix_b(pix_b),
    .hsync(hsync),
    .vsync(vsync)
    );
    // 这就是输入了
    
endmodule

```

Testbench 不需要module输入，在这里定义好每个需要使用的变量，然后下面call一下主func就好。

##### VGA 介绍

Video Graphics Array，算是一个比较早的标准现在没什么用了，代表640x480分辨率？整个接口如下

![VGA connector](/home/cyanbird/cs/blog/web/source/images/VGA_Connector.svg)

在这里我们只控制 Red, Grn, Blue, HS, 和 VS

RGB 是像素点的颜色，HS 和 VS 是像素点的位置，

Nexys4 每个存储点用了 12 bits，所以 RGB 分别是 4 嘛

VS 控制刷新频率？什么意思？

The VS signal defines the “refresh” frequency of the display



ref:

https://www.runoob.com/w3cnote/verilog-testbench.html



