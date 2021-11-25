---
title: c 语言多线程
date: 2021-11-23 20:55:09
categories:
tags:
- c语言
---

这个是跟着 https://github.com/Pithikos/C-Thread-Pool 学习的，感觉比学校的更加舒服一些！



这里涉及了一些之前没有接触过的

```c
typedef struct bsem {
	pthread_mutex_t mutex;
	pthread_cond_t   cond;
	int v;
} bsem;
```

https://www.runoob.com/cprogramming/c-typedef.html

C 语言提供了 **typedef** 关键字，您可以使用它来为类型取一个新的名字。

```
typedef unsigned char BYTE;
```

在这个类型定义之后，标识符 BYTE 可作为类型 **unsigned char** 的缩写，例如：

```
BYTE  b1, b2;
```

所以这里就是 `bsem` 是这个 struct 的缩写，你可以直接使用 bsem 来定义一个 ptr 是这个 struct



一些要用到的

https://blog.csdn.net/zmxiangde_88/article/details/7998458

`pthread_mutex_t` 这个定义一个 mutex 锁，大概可以直接定义，然后直接用 lock 锁上，再用 unlock 解锁。

![](https://i.imgur.com/Yc9fSTl.png)

这里创造的是普通锁

 PTHREAD_MUTEX_TIMED_NP，这是缺省值，也就是普通锁。当一个线程加锁之后，其他请求锁的线程将造成一个等待队列，并在解锁后按优先级得到锁。这种锁策略保证了资源分配的公平性。

```c
pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;
```

**Or**

```c
pthread_mutex_t lock;
pthread_mutex_init ( &lock, NULL);
```

这两个是近乎等价的，为了更快捷，使用第一个

https://stackoverflow.com/questions/14320041/pthread-mutex-initializer-vs-pthread-mutex-init-mutex-param





`pthread_cond_t` 这个就比较奇怪了，在学校 lab 上是和

```c
pthread_cond_t queue_cond=PTHREAD_COND_INITIALIZER;
while(isempty(work_queue)){  
			pthread_cond_wait(&queue_cond,&queue_mutex);
		}
pthread_cond_broadcast(&queue_cond);
```

一起用的，第一个是创建这个变量

条件变量的2个函数线程

```
int pthread_cond_wait(pthread_cond_t *restrict cond,
           pthread_mutex_t *restrict mutex);
int pthread_cond_signal(pthread_cond_t *cond);
```

pthread_cond_wait：

- 调用此函数时点的处理：

  1，给互斥锁解锁。

  调用此函数的线程投入睡眠，直到另外某个线程就本条件变量调用pthread_cond_signal。

- 被唤醒后的处理：返回前从新给互斥锁加锁。

- pthread_cond_signal：唤醒调用pthread_cond_wait函数的线程

所以为什么要使用这个条件啊，直接 mutex 不好吗

https://blog.csdn.net/m0_37621078/article/details/89766449

虽然是 c++ 不过凑合一下，就是说如果只有 mutex，大家都来查询它，那么这么多线程一起动弹会导致资源很大的浪费，这就引入了条件变量来解决该问题：条件变量使用“通知—唤醒”模型，只有一个线程醒着负责检查是否符合条件，如果符合就唤醒一个/多个线程来工作，其他时候都保持睡眠节省资源。

c 的使用看这个 https://www.cnblogs.com/yjds/p/8598881.html

无论哪种等待方式，都必须和一个互斥锁配合，以防止多个线程同时请求pthread_cond_wait()，这个原来是互斥的？哦，是等待判定的时候是互斥的

```c
pthread_mutex_lock(&queue_mutex);
while(isempty(work_queue)){  
pthread_cond_wait(&queue_cond,&queue_mutex);
}
conn_sock=work_queue->head->item;
dequeue(work_queue);
pthread_mutex_unlock(&queue_mutex);
```

看学校的例子，一个线程先拿到 queue 的锁，然后判定条件不满足，就调用 wait 沉睡，**这时候释放 queue mutex，在 queue cond 上排队**，所以不能同一时间有多个线程一起排队，会混乱的。然后被通知（signal/broadcast），再次竞争 mutex，cond 队列第一优先的获得 mutex，然后其他的再次进入 wait 状态。



这里先自己构建一个尝试一下

```c
typedef struct job{
    int head;
    int verbose;
    int packet;
} job;

typedef struct jobQueue{
    job *head;
    job *tail;
} jobQueue;
```

job 包含要传入的参数，然后在 job queue 定义由 job 组成的 queue，注意 head 和 tail 是传入指针，不是直接传入 job 本身，不然初始化成 NULL 会报错的。



`pthread_create(&tid[i], NULL, ana, NULL);`

这个第三个参数是一个函数指针

https://www.runoob.com/cprogramming/c-fun-pointer-callback.html

`int (*fun_ptr)(int,int);` 



fun_ptr 是一个指向函数的指针，参数是 (int, int)， 返回类型是 int

typedef  返回类型(*新类型)(参数表)

https://blog.csdn.net/qll125596718/article/details/6891881

![](https://i.imgur.com/jTnAa3e.png)

 typedef的功能是定义新的类型。第一句就是定义了一种PTRFUN的类型，并定义这种类型为指向某种函数的指针，这种函数以一个int为参数并返回char类型。后面就可以像使用int,char一样使用PTRFUN了。



当然还是需要malloc 一下的

```cmake
project(poolTest)
cmake_minimum_required(VERSION 3.20)
set(CMAKE_CXX_STANDARD 99)
set(THREADS_PREFER_PTHREAD_FLAG ON)
find_package(Threads REQUIRED)
add_executable(test main.c ana.c dispatch.c jobqueue.c)
target_link_libraries(test Threads::Threads)
```

这个是 cmake 文件，到时候要好好学习怎么写 cmake。

嗯这次写好了存在 poolTest 里，一会儿自己看看吧。