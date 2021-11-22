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

可以允许多于 1 的 un ACK 包同时存在

#### Go-Back-N（GBN）

![](https://i.imgur.com/rQQjK7q.png)

![](https://i.imgur.com/fp6YYpL.png)

这个实际理解起来比较复杂……大概 rsv 只会接收比现在 ack 多 1 的 pkt, 而 sender 只会根据最大的 ack 来调整自己的窗口 size，如果较小的 ack 丢失，会直接忽略并且继续 extend 窗口 size



#### Selective Repeat (SR)

rsv 不关注顺序，只是给每一个包单独发送 ACK

sender 负责给每个 time out 的包计时，如果超时没有获得 ACK，重新发送

![](https://i.imgur.com/KTL30mZ.png)

![](https://i.imgur.com/4ZgKAER.png)



rsv 可以接收 out of window 的数据包，如果它发送的 ACK 没有被接收

当 sender 最小是 n，rsv 最小是 m，窗口是 M，那么

m-n <= N



**window size** 最多可以有 2N 个不同的包存在，所以 windows size 应该是 half of the seq number

![](https://i.imgur.com/L1lADWg.png)

#### TCP number

![](https://i.imgur.com/Lf8zpDL.png)



TCP number, 每一块第一个 bit 的编号



TCP 为了节省，把 ACK 加入了数据包中

![](https://i.imgur.com/xt48YVB.png)



A segment can carry data and serve as an ACK

几种丢包：rsv 的 ACK 丢包

![](https://i.imgur.com/KKCOWce.png)

因为 ACK 是连续的，所以中途丢了一个 ACK 包不要紧

![](https://i.imgur.com/VVhhDya.png)



如果 ACK delay，会重传但是 rsv 会忽略

![](https://i.imgur.com/RLv5vh1.png)



##### TCP fast retransmit

如果中途 sender 丢了一个包，rsv 会重复发送最后一个正确接收的包，重复三次之后 sender 就会重新发送

ACK 带着的 seq 就是希望下一个 sender 发送的第一个 seq



Upon receiving 3 duplicate ACKs for a segment, TCP sender retransmits that segment without waiting for timeout 

![](https://i.imgur.com/fW9kHa5.png)



TCP 包里重要的三个

![](https://i.imgur.com/RRHiY98.png)

seq num: 这个包第一个 byte 的 seq num

ACK num：希望下一个接收的包的 seq

rsv window： flow control 使用

##### flow control

data in the pipeline should not exceed the receive buffer size

- Flow control

LastByteSent - LastByteAcked <= rwnd



