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



Locking and unlocking should be performed atomically
•Atomic= non-interruptible

Modern machines provide special atomic hardware instructions to implement locks
One type atomic instructions: **test_and_set**



##### test_and_set Instruction

整个 test_and_set 会将锁置于 true，这样让其他 process 无法进入临界区

整个过程是由硬件完成的，并非代码实现，它会返回旧值再给新值赋值 true

![](https://i.imgur.com/swGeRgv.png)

![](https://i.imgur.com/nKPL8k1.png)

![](https://i.imgur.com/JyftOFe.png)



#### Software method?

硬件实现无法使用在软件编程上，所以……

有以下几种工具可以使用



##### Mutex Locks

![](https://i.imgur.com/RCbxB04.png)

![](https://i.imgur.com/zbWMT8t.png)

要记住这个是硬件实现哦！

这个做了什么呢？ lock func 会先检查这个mutex是否available，如果是 false（if语句）那么不可用，就把需要 lock 的线程加到 waiting list 里面去，再用 block 去沉睡……呃插补多吧线程，然后直到可用了再去上锁。

unlock 的话，这个 P 是别的 process，还在 waiting list 里面的，这个 process 执行完毕之后会直接再唤醒另一个等待中的 P，然后将锁 true，那就是解锁了。



看看知乎的解释： https://www.zhihu.com/question/66733477

![](https://i.imgur.com/RbV7QTS.png)

![](https://i.imgur.com/gi0UQxK.png)

![](https://i.imgur.com/MwrTSbT.png)



##### Semaphores

这个是一个 int，

**0** 是不可用

**正整数** 是可用

由硬件实现的 wait() 和 signal() 实现



wait() : 查看是否是小于等于 0，是则 -1并等待，如果是正整数就 -1

比如一开始 mutex 是 1，那么-1变成 0，继续执行 critical

如果是 0，证明有一个 process 正在执行，减1 变成 -1，等待那个 process 用 signal 加一变成 0，然后再执行

signal(): 增加1



区别在于 semaphore 可以控制有多少进程能够同时访问同一资源，当act like mutex 的时候

![](https://i.imgur.com/N0ExhRb.png)

当允许多个同时访问的时候（这里是2）

![](https://i.imgur.com/nk1yGBg.png)

semaphore 的 pseudocode 是

![](https://i.imgur.com/Vjm1rzK.png)



#### synchronisation issues

这里会讨论

##### Deadlock

A waits for B to do something. B waits for A to do something. A and B are in a deadlock.

![](https://i.imgur.com/h1J6QhM.png)

当 P0 和 P1 都想要 access 资源 S 和 Q，他们如果错开等待，每次都是加1，那么两个 P 永远无法 access。改变 waitcall 顺序可以解决这个问题

##### Starvation

一个进程永远无法访问某个资源，是 bounded waiting 的反面。优先度不够，或者多个进程等待的时候始终没被唤醒。解决方法是随机选取进程唤醒。

##### Priority Inversion

![](https://i.imgur.com/55rx7UV.png)

优先度是 HML, L 用了 mutex，所以 H 访问失败，然后 M 访问，因为 M 不需要 mutex 所以 CPU 就让 M 先执行，导致 H 被挡在后面，解决方法是使用 priority-inheritance protocol，当两个进程 compete for lock 的时候，两个进程都根据最高的优先度计算，这里就是 L 变 H。



#### Classic Synchronisation Problems

这些问题用来判断 sync 是否成功运作

##### Bounded Buffer Problem

n 个 buffer，每个 hold an item，有 producer 和 consumer，producer不应该在全满的时候 produce，consumer不应该在全空的时候 consume。

![](https://i.imgur.com/O0Uh8xo.png)

producer 将 empty 的数量减少 1，并等待 mutex。然后 mutex 可用，执行完毕之后，归还 mutex，并且增加 full 的 buffer by 1

![](https://i.imgur.com/OLCbZHf.png)



这个和 producer 反过来，其他都一样的。

##### Readers and Writers problem

一个 dataset 有两种用户，reader 和 writer。writer 可以读写，reader 只读。同时可以允许多个 reader，但是只能有一个 writer。R 与 W 不能共存，所以如果 R 一直前来，W 需要一直等待。这个版本我们无法解决 W 饥饿的问题。

rw：只允许一个 W 进入，没有 R

mutex：保护 read_count 的锁

![](https://i.imgur.com/NN0NnKK.png)

W 只关注 rw，确认没有其他的 

![](https://i.imgur.com/EspnT2Y.png)

之前一个 mutex 锁去控制只有自己可以修改 RC，然后判断自己是不是唯一的读者，如果不是，那么有别的读者正在处理和作者的关系，安心读书就好~如果不是，那么就要看是否有作者正在访问，如果是就要等待作者访问完毕，顺便用mutex将其他想要访问的读者拦截，如果没有作者访问，就减一变成 0，阻挡作者访问，然后释放 mutex，让其他读者或者访问或者排队。



part 2 就是退出的时候加上mutex，判断自己是不是唯一的读者，如果是就要负责操作读者这边的 rw 锁，由可能是其他的 R process 执行的减一，加回来之后，再将在线读者数减1，归还 mutex。



##### Dining Philosophers Problem.

饿死哲学家问题，这个实在是过于经典……

In the case of 5 philosophers, the shared data:
•Bowl of rice (data set)
•Semaphore chopstick [5] all initialized to 1
•two neighboring philosophers cannot eat at the same time

![](https://i.imgur.com/t4685Nb.png)





所以如果五个哲学家同时拿起左边的筷子，它们就会因为拿不到一双筷子饿死（大嘘



解决方法？

只允许 4 个哲学家同时落座

只有在两边筷子都 available 的情况下才能拿起筷子，然后这个 picking 动作也要放在 critical area 里面，我检查完毕拿完筷子之前你们都不许动嗷！这个大概是服务生解法

asym，奇数编号哲学家先拿起左边的筷子然后右边，偶数编号哲学家先右边再左边，这个方法真是非常哲学家

