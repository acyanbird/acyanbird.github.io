---
title: 在 vscode 里写 verilog
date: 2021-10-25 14:19:44
categories: 学校学习
tags:
- verilog
---

因为十分智障的错误被坑……我觉得 xilinx 的自带 editor 和那啥没什么大区别，atom 似乎不能用，所以用 vscode 好了

首先在插件里面安装 Verilog-HDL/SystemVerilog/Bluespec SystemVerilog，然后就可以实现高亮，但是要进一步配置还需要别的



安装 verilator

`sudo apt-get install verilator`

在扩展设置里面使用 verilator

安装 ctags，也是一样 apt 一下。





ref:

https://zhuanlan.zhihu.com/p/338497672

https://github.com/mshr-h/vscode-verilog-hdl-support

https://github.com/universal-ctags/ctags/blob/master/docs/autotools.rst

https://blog.fkynjyq.com/write-verilog-with-vscode/
