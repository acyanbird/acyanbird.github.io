---
title: Trasport layer
date: 2021-11-21 16:34:53
categories: 学校学习
tags:
---

### Automatic Repeat Request (ARQ)

#### packet loss

![](https://i.imgur.com/EW4mkIo.png)

当数据包丢失的时候，过了一段时间没有接收到 ack，sender 就会重新发送包

#### ack loss

![](https://i.imgur.com/u7Dd6XU.png)

当 ack 没有接收，sender 再次发送包 0，但是对于rsv已经存在，查看序号之后就会丢弃这个包，但是发送回 ack。



**只需要 1 和 0 作为序号，来判断上一个包是否成功接收，所以 ARQ 又是 Alternating bit protocol**



#### Draw back

Round Trip Time (RTT）

需要等待很长，在等待 ack 时间之内，link 没有被使用

![](https://i.imgur.com/CmWYxoC.png)

远远小于 1



如果不等待？ R* RTT bits more send

**Length of pipeline** 𝐿 + 𝑅×𝑅𝑇𝑇 bits

𝑅×𝑅𝑇𝑇 is called the **delay-bandwidth product** of the communicating pair and indicates the length of the pipeline

代表？最大的 un ACK 包数量在 pipeline 里面

##### How many bits in the pipeline?

这也关于 rsv 方的 buffer，比如有 B bits

所以最多的 un ACK 是 min(𝐵, 𝐿 + 𝑅×𝑅𝑇𝑇)



### Pipelined

可以允许多于 1
