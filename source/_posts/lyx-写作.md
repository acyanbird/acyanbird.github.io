---
title: lyx 写作
date: 2021-11-26 20:34:41
categories: 学校学习
tags:
---

我觉得以后应该用到的可能性还蛮大的，还是放在这里好了。



https://latexlyx.blogspot.com/2012/06/lyx.html

如果忘记了可以看看这个，ref 的插入随便找个 online generator 就可以了，不过要记住引用网站要标注进入时间，上次就因为这个被大扣分，so sad



Write a report no more than 1000 words in length (excluding references)  explaining the critical design decisions and testing of your solution.  The report should be short, mainly containing a description of your  threading strategy and implementation, a justification for your choice  of the threading model, and how you have tested your solution. (~20%)



先放在这里吧，搞定再删除...首先是其他的概述要在 min 级别，然后是 ref 要正式，要采用同一个风格，按照去年的 essay 写一下好了

那么先要 general 简述这个 project 的作用，使用 pcap 进行网络嗅探，返回可疑的攻击包，目前支持三种类型。因为网络 traffic 可能会比较大量，所以单线程不足以支撑，因此需要采用多线程。

strategy chosen: 目前可从 1 thread per package 和 thread pool 里选择。这个项目采用了 threadpool

implementation 

Thread pool keeps a set number of threads running and waiting to do something. An array is created to store id of each thread. And then use a for loop and pthread_create function, pass the function the thread should be worked on as argument.

Next, implement a queue. Queue is basically an FIFO data type, use to pointer point to head and tail node. Structure job will be the node for queue which indicate there is a packet need thread and pass parameters to threads. In order to let threads sleep and wake properly, pthread_cond and pthread_mutex is used. Let's call this mutex lock queue lock, use to protect job queue. When threads on analyse.c get queue lock and find queue is empty. This thread will be blocked and release the lock, waiting for the condition specified by cond to be signaled or broadcast to. 
