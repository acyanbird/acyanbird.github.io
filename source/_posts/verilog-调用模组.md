---
title: verilog 调用模组
date: 2021-10-12 13:31:59
categories: 学校学习
tags:
- verilog
---

因为在给出的范例代码中有调用同一个文件夹里上一个 module 的操作，于是四处寻找了一下调用的语法规范。

范例代码中的模块变量与所调用的模块的端口顺序摆放一致    

```verilog
module sevenseg(
    input [3:0] sw,
    output a,
    output b,
    output c,
    output d,
    output e,
    output f,
    output g
    );
    
   内容省略
    
endmodule
```

那么在调用的时候是这样的

```verilog
sevenseg A1 (sum[3:0],a,b,c,d,e,f,g);
```

如果不需要的话也可以省略一些端口

`sevenseg A1 (sum[3:0],a,,,d,e,,g);`

记得符号不要缺少就可以

其中 sevenseg 是 module 名称，A1 是实例名，剩下的 parameter 按照顺序排布。

也有第二种调用方法

`sevenseg A1 (.sum[3:0]([3:0] sw),.a(a),.b(b),,,,,);`

结果发现这种 implicit 调用只有 SystemVerilog 能用，还是老实第一种罢。

ref：

https://blog.csdn.net/llxxyy507/article/details/81047723
