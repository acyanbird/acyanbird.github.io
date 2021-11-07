---
title: Network course work points
date: 2021-11-07 21:06:54
categories: 学校学习
tags:
---

### Network interfaces

- [ ] Is that AP?

Internet access points, interconnection between a computer and a network.

network interface card(NIC), 这个计算机的网卡，也可能是虚拟的，localhost 就是虚拟网卡用来测试网络应用。

loopback interface (localhost)，ifconfig 命令出来的 lo

网卡有 MAC 地址，AP 也有 IP 地址

![](https://i.imgur.com/EaT2PRe.png)



具体可以看 https://www.jianshu.com/p/7b83a0070588

因为我觉得每一个都很有用，所以全部复制来就太过分了吧，自己康



重点写以前没见过的



**确认号(ack)**

**ack**：确认序号，***<u style="box-sizing: border-box;">即确认字节的序号</u>\***，更确切地说，是**发送确认的一端**所期望收到的下一个序号。

所谓的***发送确认的一端\***就是将确认信息发出的一端。比如第二次握手的**S**端就是**发送确认的一端**。

确认序号为上次接收的最后一个字节序号加1.只有确认标志位(**ACK**)为1的时候，确认序号才有效。



![](https://i.imgur.com/o3YJ7W6.png)



**数据偏移**

也叫首部长度，占4个bit,它指出TCP报文段的**数据**起始处距离TCP报文段的起始处有多远。

![](https://i.imgur.com/hgs2wTR.png)

由于首部中还有长度不确定的选项字段，因此数据偏移字段是必要的。



##### 几个特殊码



**紧急URG（urgent）**

当URG=1时，表明紧急指针字段有效。

它告诉系统此报文段中有紧急数据，应尽快发送（相当于高优先级的数据），而不要按原来的排队顺序来传送。



**确认ACK（acknowledgment）**

仅当ACK = 1时确认号字段才有效，当ACK = 0时确认号无效。TCP规定，在连接建立后所有的传送的报文段都必须把ACK置为1。



**同步SYN（synchronization）**

在连接建立时用来同步序号。当**SYN=1**而**ACK=0**时，表明这是一个**连接请求报文段**。对方若同意建立连接，则应在响应的报文段中使SYN=1和ACK=1。

因此**SYN=1**就表示这是一个连接请求或连接接受报文。



**终止FIN（finis，意思是“完”“终”）**

用来释放一个连接。当FIN=1时，表明此报文段的发送发的数据已发送完毕，并要求释放运输连接。



**窗口**

占2字节。窗口值是(0，216 -1)之间的整数。

窗口指的是发送本报文段的一方的接受窗口（而不是自己的发送窗口）,窗口大小是给对方用的。

窗口值告诉对方：从本报文段首部中的确认号算起，接收方目前允许对方一次发送的数据量（以字节为单位）。



##### 选项

长度可变，最长可达40个字节。当没有使用“选项”时，TCP的首部长度是20字节。



最大报文段长度

最大报文段长度(MSS:Maximum Segment Size)表示TCP传往另一端的最大块数据的长度。当一个连接建立时，连接的双方都要通告各自的MSS。



### SYN Attack

P168，翻了我老半天了

就是攻击者通过同时发送许多 syn 连接，让服务器维持许多半开连接等待 ack

![](https://i.imgur.com/QNAiB51.png)

normal state

![](https://i.imgur.com/5PAJ7zk.png)



Coursework asks you count the total number of SYN packets
and the number of them having a unique source IP address.



使用 syn cookie 来记录之前的信息，保证不受攻击 https://zh.wikipedia.org/wiki/SYN_cookie



### ARP cache poisoning attack

首先来回答

#### How MAC addresses work?

 https://www.zhihu.com/question/21546408

看这个很棒的唷

首先，每一台设备都有一个IP地址。通信双方使用IP地址进行通信。但问题来了，网络层工作在链路层之上。要想通信，还得需要MAC地址。怎样才能得到目标的MAC地址呢？这就需要用到ARP协议。

每当电脑在通信之前会先在网络上发一个广播（这个广播不会被转发到其他网络），问谁的IP地址是某某某。这个时候对应的电脑就会回包说是我，我的MAC地址是某某某。发送方就拿到了接收方的MAC地址，进而完成通信。

![](https://i.imgur.com/xkFuOML.jpg)

如果目标电脑在另一个网络，这个时候发送方就会在自己的网络里广播说谁的IP是192.168.1.1（我们假设它的默认网关IP是192.168.1.1），网关就会回复自己的MAC。然后，发送方就会通过网关的MAC地址给网关发一条IP报文，报文的目标IP就是接收方的IP地址，来源IP自然是发送方的IP地址。在IP网络中，如果目标在同一网络，则通过广播通信；否则，通过网关转发



如何判断在同一网段？通过 IP 和子网掩码，将 mask 转换成 2 进制，然后做 AND 操作，如果一致就是在同一个网段



ARP 欺骗就好说了，直接在询问地址的时候返回自己的地址，完成欺骗，好吧其实复杂一点的



**简单案例分析**：

这里用一个最简单的案例来说明ARP欺骗的核心步骤。假设在一个LAN里，只有三台主机A、B、C，且C是攻击者。

1. 攻击者聆听局域网上的MAC地址。它只要收到两台主机洪泛的ARP Request，就可以进行欺骗活动。
2. 主机A、B都洪泛了ARP Request.攻击者现在有了两台主机的IP、MAC地址，开始攻击。
3. 攻击者发送一个ARP Reply给主机B，把此包protocol header里的sender IP设为A的IP地址，sender mac设为攻击者自己的MAC地址。
4. 主机B收到ARP Reply后，更新它的ARP表，把主机A的MAC地址（IP_A, MAC_A）改为（IP_A, MAC_C）。
5. 当主机B要发送数据包给主机A时，它根据ARP表来封装数据包的Link报头，把目的MAC地址设为MAC_C，而非MAC_A。
6. 当交换机收到B发送给A的数据包时，根据此包的目的MAC地址（MAC_C）而把数据包转发给攻击者C。
7. 攻击者收到数据包后，可以把它存起来后再发送给A，达到偷听效果。攻击者也可以篡改数据后才发送数据包给A，造成伤害。 



最理想的防制方法是网上内的每台计算机的ARP一律改用静态的方式，不过这在大型的网上是不可行的，因为需要经常更新每台计算机的ARP表。

另外一种方法，例如[DHCP snooping](https://baike.baidu.com/item/DHCP snooping)，网上设备可借由[DHCP](https://baike.baidu.com/item/DHCP)保留网络上各计算机的MAC地址，在伪造的ARP数据包发出时即可侦测到。此方式已在一些厂牌的网上设备产品所支持。

