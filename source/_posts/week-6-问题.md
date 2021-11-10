---
title: week 6 问题
date: 2021-11-05 22:49:47
categories: 学校学习
tags:
- 学校问题
---



OS：

- [ ] ```c
  conn_sock=accept(servSocket,NULL,NULL);
  ```

https://pubs.opengroup.org/onlinepubs/009696699/functions/accept.html

*address*

Either a null pointer, or a pointer to a **sockaddr** structure where the address of the connecting socket shall be returned

这个shall be returned是啥

- [ ] free() 释放的是什么，里面存储的数据？还是释放了所有被占有的空间？如果是空间那么为什么这个 ans 还可以继续使用，一个 int 大小的空间是reserve的吗

- [ ] 为什么 ans 不用 malloc?

  ```c
    int *ans;
  
    for (i = 0; i < argc-1; ++i) {
        pthread_join(threads[i], (void **)&ans); // the return value is stored in ans
        printf("Sum of integers up to %d is %d\n",limit[i], *ans);
        free(ans);
      }
  ```

- [ ] 为什么 sum 不用转回到 void *?如果添加转回似乎没有变化

```c
void *sum_runner(void *limit){
  int i;
  int *limit1=(int *)limit;
  int *sum=(int*)malloc(sizeof(int));
  for(i=1; i <= *limit1; i++){
    *sum+=i;
  }
  return((void *)sum);
}
```

why don't need sum = 0 first?

- [ ] ```c
  int *limit=(int *)malloc((argc-1)*sizeof(int));
  ```

应该是 -1 才对吧？

- [ ] ```c
  pthread_create(&tid,NULL,handle_conn,(void *)sock_ptr);
  ```

what is tid? it is not signed before, it's signed here? thread addr?



thread 会分享global val，不过子进程不会

so whats different between multi thread and fork?



Overall speed up:

1/(s+(1-s/n))

**阿姆达尔定律**（英语：Amdahl's law，Amdahl's argument），一个计算机科学界的[经验法则](https://zh.wikipedia.org/wiki/經驗法則)，因[吉恩·阿姆达尔](https://zh.wikipedia.org/wiki/吉恩·阿姆達爾)而得名。它代表了[处理器](https://zh.wikipedia.org/wiki/中央處理器)[并行运算](https://zh.wikipedia.org/wiki/並行運算)之后效率提升的能力。



Mapping between user and kernel thread

many to one, many user to one kernel

因为一个 user 需要read文件（比如），需要阻止其他用户访问，所以需要它们都在一个thread里面



implement thread pool!



https://stackoverflow.com/questions/14791278/threads-why-must-all-user-threads-be-mapped-to-a-kernel-thread



可以复用lab里面的queue！

##### Digital

反正都在 lab 里面了

为什么 hsync 是 1？

分辨率不合适屏幕的时候会发生什么？

Any better way to make inst arguments?

