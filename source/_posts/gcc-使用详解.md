---
title: gcc 使用详解
date: 2021-10-18 21:25:10
categories: 折腾
tags: 
-c
---

gcc -o <更改名称> 源文件

如果不加 -o 也可以执行，但是输出文件将会是 a.out，所以可以改名字，所以作用名的后缀其实无所谓啦（摊爪

这个在源文件的前面或者后面都可以

gcc 源文件 -lm // 连接 math 库

-l 之后紧跟库的名称，math 就是 m，不能加空格，也一定要在源文件后面。

```bash
gcc -c main.c
```

会输出 main.o，不会输出 executable file，具体咋个连接法我会看视频的。然后可以将多个 .o 连接起来

`gcc main.o sum.o sum_squares.o -o numbers`

这样就是多个程序一起编译了

如果需要加入其他的lib，在后面加上 -lm，like

```bash
gcc -c main.c prime.c -lm
// 你得到了 main.o prime.o
gcc main.o prime.o -o findPrime -lm
// 记得这里也要加上 -lm 
```



这个其实是自动生成 makefile，具体的 makefile 是

```makefile
target: dependencies
<   tab   >action   
```

lab1的例子是用 main.c sum.c sum_squares.c 编译一个 number，那么就是

```makefile
numbers: main.o sum.o sum_squares.o
<tab>gcc main.o sum.o sum_squares.o -o numbers

sum.o: sum.c
<tab>gcc -c sum.c

sum_squares.o: sum_squares.c
<tab>gcc -c sum_squares.c

main.o: main.c
<tab>gcc -c main.c
```

这个 tab 要换成 tab 键啊，别直接留着这个尖括号！

ref:

https://www.runoob.com/w3cnote/gcc-parameter-detail.html
