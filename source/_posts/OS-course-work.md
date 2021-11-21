---
title: OS course work
date: 2021-11-16 13:20:09
categories: 学校学习
tags:
- c语言
---

目前无法读取 netinet，用 linux 下的替代

 https://www.jianshu.com/p/dd1cbfa44012



记得 apt pcap

`sudo apt install libpcap-dev`



GCC 不会符合任何一个 ISO 标准，惊不惊喜，意不意外啊



PCAP exposes a domain specific language which allows you to specify packet filters. **This feature should not be used to complete the coursework. You must  implement the logic to access and process packet headers manually.** A central aim of this coursework is for you to become familiar with the network stack; marks will be deducted from solutions that rely on  external parsing or filtering logic.

不可以使用 PCAP filter 逻辑，哎呀反正这是啥我也不知道

### 测试环境

ssh -p 3217 root@localhost

连接到有 root 权限的虚拟机，我现在还是没摸透……算了，慢慢嗅呗



### 各个文件的职责

main.c 负责 call

sniff.c 负责捕捉，将 TCP IP ehter 的包赋值到 struct 上，以便后面解析，

analysis.c 使用 analysis func to determine if pakacge is malicious, currently nothing

dispatch.c 并行 call analysis，想要高分肯定要用 thread pool 啦（笑



### 输出

```
../build/idsniff invoked. Settings:
        Interface: eth0
        Verbose: 0
SUCCESS! Opened eth0 for capture
==============================
Blacklisted URL violation detected
Source IP address: 10.0.2.15
Destination IP address: 212.58.237.249
==============================
==============================
Blacklisted URL violation detected
Source IP address: 10.0.2.15
Destination IP address: 142.250.200.3
==============================
==============================
Blacklisted URL violation detected
Source IP address: 10.0.2.15
Destination IP address: 142.250.200.3
==============================
^C
Intrusion Detection Report:
4 SYN  packets detected from 1 different IPs (syn attack)
2 ARP responses (cache poisoning)
3 URL Blacklist violations
```

输出应该是这样，检测网络包，并且标注每一个的攻击类型，这次检测的是三个层次

![](https://pic1.zhimg.com/80/v2-3e35b246648f4ac393f2ad7eb0d406e8_720w.jpg)

TCP/IP/ehternet，transport，network，link



### 多进程

For this work we will focus on POSIX threads you were introduced to  in lab 3. In order to use POSIX threads, the lpthread linker flag must  be added to the project makefile like so (should be done already):

```
LDFLAGS := -lpthread -lpcap
```

嗯，要用标准进程，谁知道是啥标准哈

大概是一个搞多进程的库吧



### 头文件

这里都有 .h 的头文件，所以要了解一下

头文件是扩展名为 **.h** 的文件，包含了 C 函数声明和宏定义，被多个源文件中引用共享。有两种类型的头文件：程序员编写的头文件和编译器自带的头文件。

在程序中要使用头文件，需要使用 C 预处理指令 **#include** 来引用它。前面我们已经看过 **stdio.h** 头文件，它是编译器自带的头文件。

引用头文件相当于复制头文件的内容，但是我们不会直接在源文件中复制头文件的内容，因为这么做很容易出错，特别在程序是由多个源文件组成的时候。

A simple practice in C 或 C++ 程序中，建议把所有的常量、宏、系统全局变量和函数原型写在头文件中，在需要的时候随时引用这些头文件。



https://www.runoob.com/cprogramming/c-header-files.html

```
#include <file>
```

这种形式用于引用系统头文件。它在系统目录的标准列表中搜索名为 file 的文件。在编译源代码时，您可以通过 -I 选项把目录前置在该列表前。

```
#include "file"
```

这种形式用于引用用户头文件。它在包含当前文件的目录中搜索名为 file 的文件。在编译源代码时，您可以通过 -I 选项把目录前置在该列表前。

以前一直都不知道鸭……

#### 只引用一次头文件

如果一个头文件被引用两次，编译器会处理两次头文件的内容，这将产生错误。为了防止这种情况，标准的做法是把文件的整个内容放在条件编译语句中，如下：

```
#ifndef HEADER_FILE
#define HEADER_FILE

the entire header file file

#endif
```

这种结构就是通常所说的包装器 **#ifndef**。当再次引用头文件时，条件为假，因为 HEADER_FILE 已定义。此时，预处理器会跳过文件的整个内容，编译器会忽略它。



### PCAP

https://stackoverflow.com/questions/21921020/pcap-reading-tcp-header-fields-fails-silently

如果要解析 tcp ip，需要添加偏移量

解析看

https://www.codeleading.com/article/61202964299/



在[计算机](https://zh.wikipedia.org/wiki/计算机)[网络管理](https://zh.wikipedia.org/wiki/网络管理)领域，**pcap**（全称：packet capture）是一个用于[捕获网络流量](https://zh.wikipedia.org/wiki/數據包分析器)的[应用程序接口](https://zh.wikipedia.org/wiki/应用程序接口)（API）。pcap是用[C语言](https://zh.wikipedia.org/wiki/C语言)编写的。在[类Unix系统](https://zh.wikipedia.org/wiki/类Unix系统)中透过libpcap[库](https://zh.wikipedia.org/wiki/函式庫)来实作pcap.



这里使用了 pcap_open_live() and pcap_next()

 pcap_open_live() 

pcap_open_live - open a device for capturing，

```c
pcap_t *pcap_open_live(const char *device, int snaplen,
               int promisc, int to_ms, char *errbuf);
```

**snaplen** specifies the snapshot length to be set on the handle.

**promisc** specifies if the interface is to be put into promiscuous mode.

**to_ms**  specifies the packet buffer timeout, as a non-negative value, in milliseconds.  (See pcap (3PCAP) for an explanation of the packet buffer timeout.)



第一个参数是第一步获取的网络接口字符串，可以直接使用硬编码。
第二个参数是对于每个数据包，从开头要抓多少个字节，我们可以设置这个值来只抓每个数据包的头部，而不关心具体的内容。典型的以太网帧长度是1518字节，但其他的某些协议的数据包会更长一点，但任何一个协议的一个数据包长度都必然小于65535个字节。
第三个参数指定是否打开混杂模式(Promiscuous Mode)，0表示非混杂模式，任何其他值表示混合模式。如果要打开混杂模式，那么网卡必须也要打开混杂模式，可以使用如下的命令打开eth0混杂模式：
ifconfig eth0 promisc
第四个参数指定需要等待的毫秒数，超过这个数值后，第3步获取数据包的这几个函数就会立即返回。0表示一直等待直到有数据包到来。
第五个参数是存放出错信息的数组。
————————————————
版权声明：本文为CSDN博主「htttw」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/htttw/article/details/7521053



混杂模式？**混杂模式**（英语：promiscuous mode）是电脑网络中的术语。 是指一台机器的**网卡**能够接收所有经过它的数据流，而不论其目的地址是否是它。



example 是

```c
  pcap_t *pcap_handle = pcap_open_live(interface, 4096, 1, 1000, errbuf);
  if (pcap_handle == NULL) {
    fprintf(stderr, "Unable to open interface %s\n", errbuf);
    exit(EXIT_FAILURE);
  } else {
    printf("SUCCESS! Opened %s for capture\n", interface);
  }

```

第一个参数是在输入的时候就指定的网卡的名称，lo 或者 eth0，咱这里没有 eth0 估计是名称有所改变吧……就用 lo 就好，不过 lo 是测试 only 也不知道能不能实际运用

plus：果然要搞微缩版本的网鲨? 网夜鹰也可（笑



pcap_next()

read the next packet from a pcap_t，就是打开之后就读取的意味吧……

`const u_char *pcap_next(pcap_t *p, struct pcap_pkthdr *h);`

例子是 

```c
struct pcap_pkthdr header;
// pcap_pkthdr 是 type of per package，直接 assign 进去

packet = pcap_next(pcap_handle, &header);
```

这个是使用 while true 捕获包，需要用 pcap_loop

`int pcap_loop(pcap_t *p, int cnt, pcap_handler callback, u_char *user);`

It does not return when live packet buffer timeouts occur. A value of -1 or 0 for cnt is equivalent to infinity.



#### pcap_loop



这个大概是这么使用的

http://lihuia.com/libpcap%E6%A0%B8%E5%BF%83pcap_loop/

callback 就是去 call dispatch，进而 call analysis？



```c
int pcap_loop(pcap_t *p, int cnt, pcap_handler callback, u_char *user);
 5        /*参数说明：
 6             功能：循环捕获数据包,不会响应pcap_open_live()函数设置的超时时间
 7             参数 pcap_t *p: p是嗅探器会话句柄
 8             参数 cnt：cnt用于设置所捕获数据包的个数，负数的cnt表示pcap_loop永远循环抓包，直到出现错误。
 9             参数callback：是个回调函数指针，它的原型如下：
10             typedef void (*pcap_handler)(u_char *user, const struct pcap_pkthdr *h,
11                                    const u_char *bytes);
12             参数 user：用来给回调函数传递参数的，在callback函数当中只有第一个user指针是可以留给用户使用的，
13             如果你想给callback传递自己参数，那就只能通过pcap_loop的最后一个参数user来实现了*/
```

所以说回调函数应该就是用来处理这个包的函数啦

回调函数？https://segmentfault.com/a/1190000008293902

![](https://segmentfault.com/img/remote/1460000008293905)

假设我们要使用一个排序函数来对数组进行排序，那么在主程序(Main program)中，我们先通过库，选择一个库排序函数(Library  function)。但排序算法有很多，有冒泡排序，选择排序，快速排序，归并排序。同时，我们也可能需要对特殊的对象进行排序，比如特定的结构体等。库函数会根据我们的需要选择一种排序算法，然后调用实现该算法的函数来完成排序工作。这个被调用的排序函数就是回调函数(Callback function)。



注意，回调函数并不是C语言特有的，几乎任何语言都有回调函数。在C语言中，我们通过使用函数指针来实现回调函数。那函数指针是什么？不着急，下面我们就先来看看什么是函数指针。



### 函数指针的定义

函数指针虽然也是指针，但它的定义方式却和其他指针看上去很不一样，我们来看看它是如何定义的：

```objectivec
/* 方法1 */
void (*p_func)(int, int, float) = NULL;

/* 方法2 */
typedef void (*tp_func)(int, int, float);
tp_func p_func = NULL;
```

这两种方式都是定义了一个指向返回值为 **`void`** 类型，参数为 **`(int, int, float)`** 的函数指针。



```gradle
void (*p_func)(int, int, float) = NULL;
p_func = &func1;
p_func = func2;
```

上面两种方法都是合法的，对于第二种方法，编译器会隐式地将 **`func_2`** 由 **`void ()(int, int, float)`** 类型转换成  **`void (*)(int, int, float)`** 类型



好了剩下的还是自己去连接看吧，全部复制粘贴略没品了点。

好吧看起来问题是 c 语言到底是怎么编译函数的，所以才能知道函数指针是啥

https://www.cnblogs.com/CarpenterLee/p/5994681.html

看这个理解怎么编译



算了，再放个资料 http://blog.chinaunix.net/uid-10540984-id-3070572.html 到时候再改吧，看起来要先写完 handler 才能填写 call back func



#### 如何在 ctrl c 的时候 print？

https://www.runoob.com/cprogramming/c-function-signal.html

在 while 之前写上

```
signal(SIGINT,sigHandle);
```

然后 `sighandle` 就是在退出之前 call 的函数，记得要在 sigHandle 里写上 exit(1) 哦

要使用全局变量进行不同 IP 的记录，还有不同攻击数量，那么需要使用 mutex 锁来防止篡改。



#### multi thread

https://blog.csdn.net/qq_34352738/article/details/78307116

任何一个线程调用 exit 都会导致结束。

如果一切都是main来handle，那么就是单线程

如果在执行函数之前先 create 一个线程，然后在线程要执行的函数那边将线程 kill 掉

### TODO

- [ ] sniff 中 while（1）的 pcap_next，用 pcap_loop 替代
- [ ] 把几个 struct 搞定