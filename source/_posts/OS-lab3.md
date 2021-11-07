---
title: OS lab3
date: 2021-11-02 13:17:55
categories: 学校学习
tags:
- OSLab
---

哎呀，其实这次是 network lab 了

#### UDP 连接

这一次是一个 serv 和一个 client

这一个是关于 server 的

```c
/*
        demo-udp-03: udp-recv: a simple udp server
	receive udp messages

        usage:  udp-recv
*/

#include <stdlib.h>
#include <stdio.h>
#include <string.h>
#include <netdb.h>
#include <sys/socket.h>  // required for the socket(), recvfrom(), sendto()
#include <arpa/inet.h>   // required for htons()
#include <ctype.h>

#define BUFSIZE 2048
#define PORTNO 8888

int main(int argc, char **argv)
{
	struct sockaddr_in myaddr;	/* struct variable to store the address of server */
	struct sockaddr_in remaddr;	/* struct variable to store the address of client */
	socklen_t addrlen = sizeof(remaddr);		/* length of addresses */
	int recvlen;			/* # of bytes received */
	int servSocket;				/* file descriptor for the server socket */
	int msgcnt = 0;			/* count # of messages we received */
	char buf[BUFSIZE];	/* receive buffer to store the received message */
	int i;
    unsigned short port_num=(unsigned short) PORTNO;


	/* create a UDP socket, if not successful, exit with error message.
	The first argument AF_INET of socket() specifies that the socket
	is going to use IPv4 addresses, the next argument SOCK_DGRAM specifies 
	that the type of the socket is UDP, the last argument is for protocol family
	and is set to 0 in most applications. see the man page of socket(). */

	if ((servSocket = socket(AF_INET, SOCK_DGRAM, 0)) < 0) {
		fprintf(stderr,"cannot create socket\n");
		exit(1);
	}

	/* Fill up the different fields of the struct variable myaddr with relevant information so
	   that it can be used as a valid address for the server socket */

	myaddr.sin_family = AF_INET;    // this field sets the address type to IPv4
	myaddr.sin_addr.s_addr = inet_addr("127.0.0.1"); // this field sets the IP address, we use the IP address of the loopback interface, see the man page of inet_addr()
	myaddr.sin_port = htons(port_num);  // we use port number 2021, see the man page of htons()


	/* bind the socket to the specific IP address and port above. This gives the server
	a specific IP and port number which can be used by clients to
	connect to the server. Note that the socket() call assigns a random port number to the socket
	but we want to use a specific port number at the server, that's why bind() is required on the server side.*/

	if (bind(servSocket, (struct sockaddr *)&myaddr, sizeof(myaddr)) < 0) {
		printf("Error: bind failed\n");
		return 0;
	}

	/* now loop, receiving data and printing what we received */
	while (1) {
		printf("waiting on port %d\n", port_num);

		/*See the man page of recvfrom() system call. It receives data from the servSocket
		 and writes it into the buf; at most BUFSIZE bytes are read at a time. The number of
		 bytes actually read from servSocket is returned. After this call the struct remaddr
		 is filled in with the source address of the message (in this case it is the client address).
		 The call blocks the server process until some data is received*/

		recvlen = recvfrom(servSocket, buf, BUFSIZE, 0, (struct sockaddr *)&remaddr, &addrlen);
		
		if (recvlen > 0) {  // print the data if recvlen > 0
			buf[recvlen]='\0';
			printf("received message: \"%s\" (%d bytes)\n", buf, recvlen);
		}
		else
			printf("uh oh - error reading the message!\n");
		

	    /* convert the received message to upper case */
		for (i=0;i<strlen(buf);i++){
			buf[i]=toupper(buf[i]);
		}

		/* see the man page sendto(). It sends data to remaddr. The data is first written from
		buf to the servSocket which then tells the UDP to send the data to the address specified by remaddr.*/
		
		if (sendto(servSocket, buf, strlen(buf), 0, (struct sockaddr *)&remaddr, sizeof(remaddr)) < 0){
			fprintf(stderr, "Error in sendto\n");
			exit(1);
		}
	}
	/* never exits */
}

```



##### struct sockaddr

第一个是 

```c
#include <netinet/in.h>

struct sockaddr_in {
    short            sin_family;   // e.g. AF_INET
    unsigned short   sin_port;     // e.g. htons(3490)
    struct in_addr   sin_addr;     // see struct in_addr, below
    char             sin_zero[8];  // zero this if you want to
};

struct in_addr {
    unsigned long s_addr;  // load with inet_aton()
};
bind(servSocket, (struct sockaddr *)&myaddr, sizeof(myaddr)
```

这里使用了前三个 var，

```c
struct sockaddr_in myaddr;	/* struct variable to store the address of server */

...


myaddr.sin_family = AF_INET;    // this field sets the address type to IPv4
myaddr.sin_addr.s_addr = inet_addr("127.0.0.1"); // this field sets the IP address, we use the IP address of the loopback interface, see the man page of inet_addr()
myaddr.sin_port = htons(port_num);  // we use port number 2021, see the man page of htons()

```

第一个 `AF_INET` 是明确协议，`AF_INET`（又称 `PF_INET`）是 `IPv4` 网络协议的套接字类型，`AF_INET6` 则是` IPv6` 的；而 AF_UNIX 则是 Unix 系统本地通信。



第二个明确地址，因为 sockaddr_in 是双层的引用，所以这边也要引用两次……为什么呢？

inet_addr则是下一个

```c
#include <arpa/inet.h>

in_addr_t inet_addr(const char *cp);
char *inet_ntoa(struct in_addr in);

myaddr.sin_addr.s_addr = inet_addr("127.0.0.1"); 
//这里使用的 cp 就是按照 IPV4 的协议走，这个是将IPV4地址转换


```

第三个是端口设置

```c
myaddr.sin_port = htons(port_num);

// 如果是小尾端存储的机器，转换成大尾端

The htons() function converts the unsigned short integer hostshort from host byte order to network byte order. 
```

至于为什么要转换order呢？ 网络字节是大端字节顺序，如果机器是小端字节，那就要 change

CSAPP 说过有小尾端和大尾端

在Linux和Windows网络编程时需要用到htons和htonl函数，用来将主机字节顺序转换为网络字节顺序。

在Intel机器下，执行以下程序
int main()
{
printf("%d /n",htons(16));
return 0;
}

得到的结果是4096，初一看感觉很怪。

解释如下，数字16的16进制表示为0x0010，数字4096的16进制表示为0x1000。 由于Intel机器是小尾端，存储数字16时实际顺序为1000，存储4096时实际顺序为0010。因此在发送网络包时为了报文中数据为0010，需要经过htons进行字节转换。如果用IBM等大尾端机器，则没有这种字节顺序转换，但为了程序的可移植性，也最好用这个函数。

另外用注意，数字所占位数小于或等于一个字节（8 bits）时，不要用htons转换。这是因为对于主机来说，大小尾端的最小单位为字节(byte)。



不记得大小尾看这里 https://blog.csdn.net/chy555chy/article/details/51966160 ，简单来说就是数据的高字节保存在内存的高地址中，而数据的低字节保存在内存的低地址，还是相反，只有在 byte 下面会工作哦~

##### socket(AF_INET, SOCK_DGRAM, 0)

这个的代码是

```c
if ((servSocket = socket(AF_INET, SOCK_DGRAM, 0)) < 0) {
		fprintf(stderr,"cannot create socket\n");
		exit(1);
	}
// 检测连接是否成功
```

原型是

```c
int socket(int domain, int type, int protocol);    
```

domain: 通讯标准（大概吧），之前是AF_INET所以就继续

type:  specifies the communication semantics，通讯语义？TCP 使用 sock_dtream,UDP 使用 sock_dgram 居多，一个可靠双工，一个就送一次

TCP (`SOCK_STREAM`) is a connection-based protocol.  The  connection is established and the two parties have a conversation until  the connection is terminated by one of the parties or by a network  error.

两边维持通信，直到一方关闭或者遇到错误

UDP (`SOCK_DGRAM`) is a datagram-based protocol.  You send one datagram and get one reply and then the connection terminates.送一次，得到回复，关闭

protocol：制定应该使用哪一个协议，通常来说一个 domain 只有一个协议，所以设置为 0 就可以

它会返回一个文件描述符，如果成功打开的话是个非负整数，具体请看：https://blog.csdn.net/qq_26222859/article/details/52068615

跟stream不一样，它是整数而不是指针（当真）？这也是使用文件最常用的方式



##### bind(servSocket, (struct sockaddr *)&myaddr, sizeof(myaddr)

```c
       #include <sys/socket.h>

       int bind(int sockfd, const struct sockaddr *addr,
                socklen_t addrlen);
```

嗯，下面这个是使用

```c
if (bind(servSocket, (struct sockaddr *)&myaddr, sizeof(myaddr)) < 0) {
   printf("Error: bind failed\n");
   return 0;
}
```

When a socket is created with socket(2), it exists in a name space (address family) but has no address assigned to it.  bind() assigns the address specified by addr to the socket referred to by the file descriptor sockfd.  addrlen specifies the size, in bytes, of the address structure pointed to by addr.

当socket创造的时候，name space(坑）有了，但是要用 bind 去赋值，第一个是socket返回的文件描述符，然后是myaddr的地址，强行转换成指向struct sockaddr的指针，最后是它的大小。返回的还是文件描述符，所以



下一个是收听端口了

`recvlen = recvfrom(servSocket, buf, BUFSIZE, 0, (struct sockaddr *)&remaddr, &addrlen);`

原型函数是

```c
ssize_t recvfrom(int sockfd, void *buf, size_t len, int flags,
                 struct sockaddr *src_addr, socklen_t *addrlen);
/*fd:file discriptor
*buf 指向存储接收变量的指针*/
char buf[BUFSIZE];
// 接下来是buf大小
// flag 各种报错？0就是默认吧，没问题的啦
// A parameter that can be set to 0, MSG_CONNTERM, MSG_PEEK, MSG_OOB, or MSG_WAITALL. The MSG_CONNTERM flag is mutually exclusive with other flags.
// 在call结束之后，*src_addr 和 *addrlen 会被传入信息的端口赋值
```

最后一个

```c
if (sendto(servSocket, buf, strlen(buf), 0, (struct sockaddr *)&remaddr, sizeof(remaddr)) < 0){
			fprintf(stderr, "Error in sendto\n");
			exit(1);
		}
```

很好理解了，fd在小于0就是错误，剩下的参数和上面的recvfrom一样的



##### TCP 连接

`listen(servSocket,0);`

前面是 serv，后面是pending的连接数，这个只能有一个连接，不接受等待



`conn_sock=accept(servSocket,NULL,NULL);`

把等待queue的第一个提取出来

*address*

Either a null pointer, or a pointer to a **sockaddr** structure where the address of the connecting socket shall be returned.

不太明白什么意思……下次问问

然后会创建一个新的socket连接处理这个，conn_sock 就是新的socket

read，成功之后upper case，然后write回去，结束



总之，从client和server一起写一下吧

client server 共同

创建 struct sockaddr_in ，填入 detail



~~server端 bind，创建监听端口？不对，connect和bind应该有类似之处？~~

TCP 和 UDP 都一样，myaddr创造struct， serve socket 是 file discriptor，两个分别赋值，然后 client 用 connect 结合，server 用 bind 结合。

connect 搞定三次握手，用新的 conn_sock，不同于listen的端口传递数据。write 发送，read收到，close四次挥手。



server的话先是listen创建监听端口，然后收到客户再搞出新的专属socket conn_sock，用read读取，write写回



read write 都是

extern ssize_t read(int __fd, void *__buf, size_t __nbytes)

Read NBYTES into BUF from FD.  Return the
number read, -1 for errors or 0 for EOF.

从 fd 端口读取 nbytes 到 buf，write 也是，client是 client socket，server 是 conn_socket，果然是万物皆文件啊……感叹.jpg



##### 多线程

首先需要一个 func 来规定这个 thread 要执行什么代码

```c
void *thread_code(void *arg) {
   printf("This is code being ran by thread %d\n", (long) arg);
}
```

这个 func 必须返回一个指向 void 的指针，参数值一个指向 void 的指针（我相信是 thread 的运行地址？）

void *thread_code 代表的是返回值是指针，void (\*thread_code) 才是，thread_code是指向返回值是 void 的函数的指针

```c
int pthread_create(pthread_t *thread, const pthread_attr_t *attr,
                   void *(*start_routine) (void *), void *arg);
```

给出的例子是

```c
pthread_t threads[10];
int i;
for (i = 0; i < 10; i++) {
   pthread_create(&threads[i], NULL, &thread_code, (void *) NULL);
}
```

第一个是获得这个 thread 的 id 的地址，第二个先不管，第三个是指向 **指向 func 指针** 的指针，第四个是传入的参数，是 void 指针类型，需要强制转化

&threads[1] 是 addr of second element, where second element st

为了避免成为孤儿进程，需要combine到主进程里面

```c
int pthread_join(pthread_t thread, void **retval);
// retval = return value
```

例子里的答案是这么用的

```c
for (i = 0; i < argc-1; ++i) {
      pthread_join(threads[i], (void **)&ans); // the return value is stored in ans
      printf("Sum of integers up to %d is %d\n",limit[i], *ans);
      free(ans);
```

第一个参数是 thread 的 id（一个数组），然后下一个是储存返回值，一个指向指针的指针，跟 **argv 一致，可以存储传回的多个 val



![](/home/cyanbird/cs/blog/web/source/images/1679707084.jpg)



执行是 `gcc -lpthread threaded_code.c` 跟 lab 不一样



**划重点，void *是通用指针类型，在转换到各种类型指针和转化回来的时候不会丢失数据**

##### Mutex lock

直接看代码吧

```c
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>


volatile int global_number = 0;
// 在C语言中，volatile关键字可以用来提醒编译器它后面所定义的变量随时有可能改变，因此编译后的程序每次需要存储或读取这个变量的时候，都会直接从变量地址中读取数据
// 多线程专用啾
pthread_mutex_t muxlock = PTHREAD_MUTEX_INITIALIZER;

void * thread_code(void *arg) {
  pthread_mutex_lock(&muxlock);
  global_number = global_number + 1;
  pthread_mutex_unlock(&muxlock);
  // 在增加的时候
  return NULL;
}

int main(void) {
  pthread_t threads[10];
  int i;
  for (i = 0; i < 10; i++) {
    pthread_create(&threads[i], NULL, &thread_code, NULL);
  }
  for (i = 0; i < 10; ++i) {
    void *rv;
    pthread_join(threads[i], &rv); 
  }
  printf("This value should be 10: %d\n", global_number);
  return EXIT_SUCCESS;
}
```

https://stackoverflow.com/questions/34524/what-is-a-mutex

这个解释的比较清楚，是允许某个进程说话的橡皮鸭子（

[临界区](https://zh.wikipedia.org/wiki/临界区)内部，通过[互斥锁](https://zh.wikipedia.org/wiki/互斥锁)（mutex）保证只有一个线程可以访问，因此临界区内的变量不需要是volatile的；而在临界区外部，被多个线程访问的变量应为volatile，这也符合了volatile的原意：防止编译器[缓存](https://zh.wikipedia.org/wiki/缓存)（cache）了被多个线程并发用到的变量。

嗯嗯这是 volatile 的使用

```c
pthread_mutex_t mutex1 = PTHREAD_MUTEX_INITIALIZER;
pthread_mutex_init(&mutex2, NULL);
```

这两个语句是等效的

##### 多线程 server

这个主要改得是 server

```c
void *memset(void *str, int c, size_t n)
```

- **str** − This is a pointer to the block of memory to fill.
- **c** − This is the value to be set. The value is passed as an int, but the function fills the block of memory using the unsigned char conversion of this value.
- **n** − This is the number of bytes to be set to the value.

大概是 memory set, 将 void 这块block 用 c 的值填满，c 将会变成 unsigned char，然后一共有 n 个

```c
listen(servSocket,BACKLOG);
```

一共可以排队等待 backlog 个client



我去这个是真的猛，还要包括 head 文件使用



ref:

https://www.gta.ufrj.br/ensino/eel878/sockets/sockaddr_inman.html

https://zhidao.baidu.com/question/127548969.html

https://pubs.opengroup.org/onlinepubs/009695399/functions/inet_addr.html

https://blog.csdn.net/seven407/article/details/6096702

https://man7.org/linux/man-pages/man2/socket.2.html

https://stackoverflow.com/questions/5815675/what-is-sock-dgram-and-sock-stream

https://man7.org/linux/man-pages/man2/bind.2.html

https://www.ibm.com/docs/en/zos/2.1.0?topic=functions-recvfrom-receive-messages-socket

http://c.biancheng.net/cpp/html/3042.html

https://linux.die.net/man/3/pthread_join