---
title: OSlab2
date: 2021-10-26 15:32:25
categories: 学校学习
tags:
---

要死嘞要死嘞——



##### fprintf 函数

其实 printf 便是 fprintf(stdout, "output"); 的简写，将字符串投向标准输出。

C 语言把所有的设备都当作文件。所以设备（比如显示器）被处理的方式与文件相同。以下三个文件会在程序执行时自动打开，以便访问键盘和屏幕。

| 标准文件 | 文件指针 | 设备     |
| -------- | -------- | -------- |
| 标准输入 | stdin    | 键盘     |
| 标准输出 | stdout   | 屏幕     |
| 标准错误 | stderr   | 您的屏幕 |

所以相对而言的，scanf 就是从标准输入中读取，不过这个比较复杂就没有一个直接对应的函数了



##### 文件操作：stream mode

函数	功能
fopen()	打开流
fclose()	关闭流
fputc()	写一个字符到流中
fgetc()	从流中读一个字符
fseek()	在流中定位到指定的字符
fputs()	写字符串到流
fgets()	从流中读一行或指定个字符
fprintf()	按格式输出到流
fscanf()	从流中按格式读取
feof()	到达文件尾时返回真值
ferror()	发生错误时返回其值
rewind()	复位文件定位器到文件开始处
remove()	删除文件
fread()	从流中读指定个数的字符
fwrite()	向流中写指定个数的字符
tmpfile()	生成一个临时文件流
tmpnam()	生成一个唯一的文件名



这个就差不多是……要用的，不过其实 `fopen() fclose() fputs() fgets() `用的比较多啦

```c
FILE *fp = fopen("文件名包括路经和后缀", "mode");	// 一般包括r,w,a这些，若是 fp == NULL，则打开失败

while(fgets(str,BUFSIZE,fp)!=NULL){ // read the file line by line
        fprintf(stdout,"%s",str);         // same as doing printf("%s",str);
    }	// 读取文件内容，str存储，bufsize 读取多少，最后是文件 pointer

char *fgets(char * restrict str, int size, FILE * restrict stream);	// fgets 原型

int fputs(const char *str, FILE *stream);
fputs("这是 C 语言。", fp);	// 这个是输出

fclose(fp);
fp = NULL;

// 记得要 close fp 唷~也要手动将 fp 指向 NULL 才行
```

`fputs()` 和 `fgets()` 来自 `fputc()` 和 `fgetc()` ，反正是 string 和 char 的区别嘛~ 

`fopen()` 的返回值是 0，错误则是 1。

上面的是 file pointer which is c lib, but file discriptor is system function in unix like. 

`read() write() open()` 是系统功能，它们也许也是 c 实现的，但是在使用的时候的确是 call system. 其中 [`stdin,`](http://linux.die.net/man/3/close)[`stdout`](http://linux.die.net/man/3/close), and [`stderr`](http://linux.die.net/man/3/close) 分别对应 0, 1, 2

```c
#include<stdio.h>
#include<string.h>
#include<stdlib.h>
#include<fcntl.h>  // for the open() function
#include<unistd.h> // for read(), write() and close() functions

#define BUFSIZE 10

int main(){

    int fd1=open("./textfile1.txt",O_RDONLY);  // opens the file in read only mode and returns a file descriptor for the file
    int x;

    if(fd1 < 0){
        write(2,"Error opening file\n",sizeof("Error opening file\n")); // writes the error message to stderr
        exit(1);
    }

    char str[BUFSIZE];

    printf("Content of the opened file:\n");
    while((x=read(fd1,str,BUFSIZE-1)) > 0){ // read() reads up to BUFSIZE-1 bytes from fd1 and stores in str
                                           // the number of bytes actually read is returned
            str[x]='\0';
            write(1,str,strlen(str));   // '1' is the file dscriptor for stdout, hence this is equivalent to printf("%s", str)
    }
    printf("\n");
    close(fd1);
    return 0;

}
```



在 discriptor 状态下 w 并非不存在会自己创造，所以需要

```c
int fd2 = open(name2, O_WRONLY);

    if(fd2 < 0){    // if file not exist
        fd2 = open(name2, O_CREAT, 0777);
    }
```

记住一定要在最后加入 permission 不然会错误并且不会报错的！！！是八进制所以要四位数四位数！



##### 结构

一个或多个变量的集合，格式如下

```c

struct tag { 
    member-list
    member-list 
    member-list  
    ...
} variable-list ;

struct t {...} x,y,z;
// 等价于 
int x,y,z;

//其中 tag 是结构标记，可选

struct B;    //对结构体B进行不完整声明,不需要分配存储空间
```

可以使用 结构名.成员 的方式访问，不过赋值变量到结构需要添加 struct 关键字在结构名之前，例如

```c
struct point {
    int x;
    int y;
};	//需要加上分号唷

// 结构也可以嵌套

struct rect {	// 定义一个长方形
    struct point pt1;
    struct point pt2;
};

struct rect screen;	// 定义rect成员

screen.pt1.x;	// 这样访问多重变量
```

可以生成指向结构的指针，这样访问的速度会比直接复制结构更快

```c
struct point origin, *pp;
pp = &origin;
printf("origin is (%d, %d)", (*pp).x, (*pp).y);
```



因为 . 的优先度高于 * 所以必须加括号，当然也有简写

`pp -> x`

这也是等价的

ref:

https://blog.csdn.net/qq_38374864/article/details/72794221

https://www.runoob.com/cprogramming/c-input-output.html

https://www.runoob.com/cprogramming/c-function-fputs.html

