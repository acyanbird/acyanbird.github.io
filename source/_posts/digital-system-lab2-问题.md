---
title: digital system lab2 问题
date: 2021-10-18 10:19:31
categories: 学校学习
tags:
- digitalLab
- verilog
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

a信号发生变化时触发



always@（posedge a or negedge a）

a信号双边沿触发



always

不断触发，伪组合逻辑电路



always@（*）

任意输出信号发生变化触发



always@（a or posedge clk）

clk上升沿或a发生变化触发



always@(data[2:0])

data是8位数据，前三位data[2:0]发生变化触发



##### 时序电路和组合电路

其实就是 sequential 和 combinational……

组合逻辑电路在逻辑功能上的特点是任意时刻的输出仅仅取决于该时刻的输入，与电路原来的状态无关。而时序逻辑电路在逻辑功能上的特点是任意时刻的输出不仅取决于当时的输入信号，而且还取决于电路原来的状态，或者说，还与以前的输入有关。



##### 阻塞赋值和非阻塞赋值区别

所谓的**阻塞赋值**”=”就是说， 在这个语句没有执行完之前，后面的语句是不执行的。 这里执行的含义是指完成变量值的更新。 **非阻塞赋值**”<=”是指， 所有的语句可以并发执行，而前面的值是否执行完毕不会影响后面的语句， 换句话说，语句的顺序是无关紧要的。

非阻塞赋值 5 个字比较长，所以是 <=，阻塞赋值比较短，所以是 =



##### 什么时候用阻塞赋值和非阻塞赋值？

先记住英文，blocking & nonblocking assignment

编码原则很多，就阻塞非阻塞赋值而言，新手最需要牢记的是其中三条：
1、时序逻辑一定用非阻塞赋值”<=”,一旦看到敏感列表有 posedge 就用”<=”。
2、组合逻辑一定用”=” ，一旦敏感列表没有 posedge 就用”=”，一旦看到 assign 就用”=”。
3、时序逻辑和组合逻辑分成不同的模块，即一个 always 模块里面只能出现非阻塞赋值”<=”或者”=”。如
果发现两种赋值并存，一个字”改”，心存侥幸可能会给后续工作带来更多麻烦。



##### if 语句什么时候需要使用 begin...end

在 if 后面只有单个语句的时候不需要，但是若是跟随多个操作语句，此时用begin和end这两个关键词将几个语句包含起来成为一个复合块语句。

不使用 begin...end:

```verilog
if(a>b) out1<=int1;
else if(a==b) out1<=int2;
else out1<=int3;
```

使用 begin...end:

```verilog
if(a>b)
    begin
     out1<=int1;
     out2<=int2;
    end

else
    begin
     out1<=int2;
     out2<=int1;
    end
```



##### order matter？



##### 整数数值表示方法

数字声明时，合法的基数格式有 4 中，包括：十进制('d 或 'D)，十六进制('h 或 'H)，二进制（'b 或 'B），八进制（'o 或 'O）。数值可指明位宽，也可不指明位宽。

下划线标注在数值位置增加可读性

```verilog
4'b1011         // 4bit 数值
32'h3022_c0de   // 32bit 的数值
```

以下三种写法是等效的

```verilog
counter = 'd100 ; //一般会根据编译器自动分频位宽，常见的为32bit
counter = 100 ;
counter = 32'h64 ;
```

通常在表示位宽的数字前面加一个减号来表示负数。例如：

```verilog
-6'd15  
-15
```

-15 在 5 位二进制中的形式为 5'b10001, 在 6 位二进制中的形式为 6'b11_0001。

需要注意的是，减号放在基数和数字之间是非法的，例如下面的表示方法是错误的：

```verilog
4'd-2 //非法说明
```

##### assign 语句变更

只要 RHS 上的任何操作数的值发生变化， LHS 就会使用新值进行更新。

assign 语句也称为连续赋值， 并且始终处于活动状态

##### verilog {} 大括号的使用

{} 的基本使用是两个，一个是拼接，一个是复制，下面列举了几种常见用法。

- { }表示拼接，{第一位，第二位…}；
- \{ \{ \} \}表示复制，\{4\{a\} \}等同于\{a,a,a,a\}；
   所以\{13\{1‘b1\} \}就表示将13个1拼接起来，即13’b1111111111111。

所以

`assign {a,b,c,d,e,f,g} = ~intseg;`

就是将 a,b,c,d,e,f,g 拼接，一起接受 not intseg 的赋值



ref：

https://blog.csdn.net/gududeyhc/article/details/8795202

https://blog.csdn.net/qq_33929689/article/details/51842606

https://www.cxyzjd.com/article/vagrantda/79711134

https://blog.csdn.net/qq_45467083/article/details/103368097

https://blog.csdn.net/sinat_20265495/article/details/41314577

https://blog.csdn.net/Times_poem/article/details/52032890

https://blog.csdn.net/Reborn_Lee/article/details/106985139

https://blog.csdn.net/uiojhi/article/details/108764543
