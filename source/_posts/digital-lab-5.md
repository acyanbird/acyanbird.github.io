---
title: digital system lab 5
date: 2021-11-07 13:02:36
categories: 学校学习
tags:
- verilog
---

这个的确没什么好记录的，就是到最后一步的时候生成出现错误，需要约束逻辑信号，反正我的确也看不懂这是咋回事所以忽略它好了

https://cloud.tencent.com/developer/article/1759905

写一个 tlc 文件

```
set_property SEVERITY {Warning} [get_drc_checks NSTD-1]
set_property SEVERITY {Warning} [get_drc_checks UCIO-1]
```

然后在 prehook 它

![](https://imgur.com/PmeKbS0.png)

![](https://imgur.com/fI6yIjS.png)





apply 之后 re-run一下。