---
title: Xilinx vivado 启动问题
date: 2021-11-06 19:21:21
categories: 折腾
tags:
---

结果我还老老实实跟他搞了那么久！其实它自带的 bash 也是添加到 PATH 啊！

在bash文件，本地是 ~/.bashrc 全局是 etc 什么的加入

`PATH=/tools/Xilinx/Vivado/2019.2/bin:$PATH`

再次 `. ~/.bashrc` 执行就可
