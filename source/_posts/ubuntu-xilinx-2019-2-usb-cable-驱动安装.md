---
title: ubuntu xilinx 2019.2 usb cable 驱动安装
date: 2021-10-10 21:30:24
categories: 折腾
tags:
-xilinx
---

我不知道是 ubuntu 要额外安装 lib 的操作，还是 linux 共性如此……总之，如果没有安装 cable driver 的话，那么插入 FPGA dev broad 是无法被识别的。我手上的是 NEXYS A7，所以使用的是 vivado。

总之先去安装目录里面（默认是/tools/Xilinx），找到 install_drivers 

`find -name install_drivers`

当然名字可能有些许不同，不过大概率在 cable_drivers 下面，总之是不是的……嗯你感觉是打开看看，大概就清楚了（大概吧

**记得做好备份，万一弄错了呢？**

cat 打印出来看看，嗯，是 bash 文件（打头有 #!/bin/sh）,然后 sudo 执行

`sudo bash install_drivers`

然后我反正成功了……然后重新插入一下 usb，应该就可以检测到了。
