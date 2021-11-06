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

  

