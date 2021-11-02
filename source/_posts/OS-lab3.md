---
title: OS lab3
date: 2021-11-02 13:17:55
categories: 学校学习
tags:
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





ref:

https://www.gta.ufrj.br/ensino/eel878/sockets/sockaddr_inman.html

https://zhidao.baidu.com/question/127548969.html

https://pubs.opengroup.org/onlinepubs/009695399/functions/inet_addr.html

https://blog.csdn.net/seven407/article/details/6096702

https://man7.org/linux/man-pages/man2/socket.2.html

https://stackoverflow.com/questions/5815675/what-is-sock-dgram-and-sock-stream

https://man7.org/linux/man-pages/man2/bind.2.html
