---
title: OS Synchronisation
date: 2021-11-13 23:46:59
categories: 学校学习
tags:
- OS
- c语言
---

#### Peterson’s Solution

Gary L. Peterson (1981) proposed an algorithmic solution for the critical section problem.



https://zhuanlan.zhihu.com/p/374287625

Peterson 算法是基于双线程互斥访问的LockOne与LockTwo算法而来。LockOne算法使用一个 flag  布尔数组，LockTwo 使用一个 turn的整型量，都实现了互斥，但是都存在死锁的可能。Peterson  算法把这两种算法结合起来，完美地用软件实现了双线程互斥问题。

首先这段代码

```
Pi进程：                                                                    
flag[i] = True;
while(flag[j]);
critical section;
flag[i] = False;
remainder section; 

Pj进程：                                                               
flag[j] = True;
while(flag[i]);
critical section;
flag[j] = False;
remainder section;
```

当 PI 想要访问的时候，先查看 flag[j] 是否是 true，如果是 true 那就说明 Pj 在访问，所以需要等待直到访问结束。因为两个进程是先提出要访问 flag = True，所以当两个都是 true 的时候品德高尚的俩进程就会互相谦让，然后饿死在门口。



Peterson算法就是在上面代码的基础之上，又引入了一个变量turn，打破了这种因为谦让而导致“饥饿”的现象。下面我们先来看看Peterson算法的代码：

```
Pi进程：                                                                   
flag[i] = True;
turn = j;
while(flag[j] && turn == j);
critical section;
flag[i] = False;
remainder section;

Pj进程：                                                                   
flag[j] = True;
turn = i;
while(flag[i] && turn == i);
critical section;
flag[j] = False;
remainder section;
```

怎么理解变量turn呢？可以将turn变量理解成轮到谁进入临界区了。举个例子：turn = i，表示轮到Pi进入临界区。那么上面这个代码就可以理解为：首先，Pi想进入临界区（`flag[i] = True`），然后，还是和前面的代码一样，Pi会先把进入临界区的机会让给Pj（`turn = j`），同样地，当Pj想进入临界区时，也会将进入临界区的权利先让给Pi。紧接着，变量turn的作用就显现出来了，当Pj把进入临界区的机会又让给Pi的时候（注意：这是发生在Pi将进入临界区的优先权让给Pj之后），Pi这次就会直接进入临界区。就不会再次出现一直互相谦让，最终导致均无法进入临界区的情况了。



关于为什么当进入临界区的权利（即turn = i）又回到Pi手里时，Pi会直接进入临界区的分析？我们可以分析一下Pi能够成功进入临界区的条件（即：while(flag[j] && turn == j)语句）：

总的分为以下两种情况：

1. `Pj`不想进入临界区（flag[j] = False）

当`Pj`不想进入临界区时，自然也就不存在Pi和Pj冲突的情况，Pi当然就直接进入临界区。

1. `Pj`想进入临界区（flag[j] = True）

当`Pj`想进入临界区，又分为以下两种情况：

- 当 turn = i

turn = i说明当前轮到i进入临界区了 ，这个时候i就直接进入临界区了，不再谦让。（其实这个挺合理的，根据Peterson算法的代码我们不难发现因为turn的值是根据先后想要进入临界区的顺序排列的）

- 当 turn != i

turn != i 说明当前轮到i进入临界区了没有轮到Pi进入临界区，Pi自然需要等待。

仅过上面的分析，我们就不难理解，当Pi和Pj经过一轮谦让之后，就会直接根据turn的值（即：该轮到谁进临界区了）来直接决定谁该进入临界区。现在回过头回顾整个算法，其实我们会发现，Peterson算法的思想会更贴近于生活中的真实情况，大家一般都是略微谦让一下，然后直奔主题，难道不是吗？哈哈



But it is not perfect
•Employs Busy wait
•May fail in Modern architectures



In modern computer architectures independent read and write operations may be reordered.

https://stackoverflow.com/questions/15675676/will-petersons-solution-work-correctly-on-modern-cpu-architectures

On a system with one CPU, Peterson's algorithm is guaranteed to work, because program's own behavior is observed in program order.

On systems with multiple CPUs the algorithm may fail to work because  the program order of events occurring on one CPU may be perceived  differently on another.

That may cause early entering into the critical section (while it's  still in use by another thread) and early exiting from the critical  section (when the work with the shared resource hasn't been finished  yet).

For this reason one needs to ensure that the order of events  occurring inside the CPU is seen the same outside. Memory barriers can  ensure this serialization.



多核 CPU 会导致两个 process 之前的 assign 执行时间不一致，在某个 process 完成两个赋值之前就进入了 critical section, 导致另一个 process 也可以进入。



#### Solution to CS Problem Using Locks

All solutions are based on idea 
of locking
•Two processes can not have a 
lock simultaneously. 

![](https://i.imgur.com/KKGjrdF.png)



