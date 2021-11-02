---
title: OS lab1
date: 2021-10-18 22:50:50
categories: 学校学习
tags: 
- c
- OSLab
---

还是太乱了啊……所以就直接以 lab 命名吧，反正涉及很多小项的。

ex3，传入一个数组到 func

pointer 和 array 的关系在 c 程序设计的 5.3 中涉及到了，由于数组的定义其实本质上是指向整个数组连续存储的第一个，然后可以通过指针访问，eg：

```c
int a[10]; // declare an array length 10
int *pa;
pa = a[0];
// 这将等价于
pa = a;
// 所以引用也是一致
x = a[0]; == x = *pa;
x = a[1]; == x = *(pa+1);
```

我的排序果然一团糟……使用 insert sort 的话代码如下

```c

#include <stdio.h>

// write the code for the sort() function that sorts an integer array in ascending order
int sort(int x[], int size){
    // array is like pointer, input the first val ptr, so it will be changed
    // insert sort
    int tmp, j;
    // declare j here, or it can't be used outside the loop
    for(int i = 1;i < size;i++){
        tmp = x[i];
        for (j = i-1; j >= 0; j--) {
            if(tmp < x[j]){
                // move backward
                x[j+1] = x[j];
            } else{
                // end the loop since tmp in right place
                break;
            }

        }
        x[j+1] = tmp;
    }
    return 0;
}


int main() {
    int x[]={4,1,4,3,10,5};
    int i;

    sort(x,6); // sort() function sorts the array x in ascending order

    printf("The sorted array is as follows:\n");

    for(i=0; i<6; i++){
        printf("%d ",x[i]);
    }
    printf("\n");
    return 0;
}
```



要注意 j 的 declare 位置，要在 func 的前面 declare，不然它不能在后面被使用，看来要将所有排序都写一次才行……



```c
size_t size
```

size_t 好像是 size 的类型？unsigned data type，可以存储所有的 array

~~还是不知道，记得去问。~~

是的，这个 size_t 就是一种类型，于 int double 什么的类似。



`p = (int *) malloc (sizeof(int) * 10);`

这个意思是赋予 p 一个大小为 10 int 的空闲块，malloc 函数返回的是 void * 类型，如果你写成：p = malloc (sizeof(int)); 则程序无法通过编译，报错：“不能将 void* 赋值给 int * 类型变量”。所以必须通过 (int *) 来将强制转换。 这就是之前那个括号的意思。

calloc() 函数用来动态地分配内存空间并初始化为 0，其原型为：
   void* calloc (size_t num, size_t size);

 calloc() 在内存中动态地分配 num 个长度为 size 的连续空间，并将每一个字节都初始化为 0。所以它的结果是分配了 num*size 个字节长度的内存空间，并且每个字节的值都是0。不然会出现很奇怪的情况的，其他使用和 malloc 是一样一样的~ 



##### 使用 debugger

使用的是 GNU Debugger (GDB), 在编译的时候加入选项

`gcc -g -o helloworld helloworld.c`

然后

`gdb ./helloworld`

会给予你 interact interface，你输入 run 可以显示 debug 内容，这次的 example 是

```c
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char *argv[]) {
   int *my_array = (int *) malloc(5 * sizeof(int)); 
   int i;
   for (i = 0; i < 1000000; i++) { 
      my_array[i] = i;
   }
   free(my_array);
}
```



Program received signal SIGSEGV, Segmentation fault, 存储器区段错误 (Segmentation fault)。应该来说就是访问了不属于这个程序被 assign  的存储区块，可以通过修改 malloc 得到（唉其实没有 gdb 也会终止程序？哦，不会）



ref：

https://blog.csdn.net/xw13106209/article/details/4962479

http://c.biancheng.net/cpp/html/134.html
