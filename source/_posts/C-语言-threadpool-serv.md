---
title: C 语言 threadpool serv
date: 2021-11-09 13:39:35
categories: 学校学习
tags:
- c语言
---

这个是 create 固定数量的线程，然后当有新进程加入的时候唤醒某一个执行。



先创造一个 queue 去维持所有进程，初始化两个条件，然后每个线程会开始执行它的 func，

```c
#include <stdlib.h>
#include <stdio.h>
#include <string.h>
#include <netdb.h>
#include <sys/socket.h>
#include <arpa/inet.h>
#include <ctype.h>
#include <unistd.h>
#include <pthread.h>
#include "queue.h"

#define BUFSIZE 2048
#define BACKLOG 500
#define NUMTHREADS 10
#define PORTNO 8888


/* Queue where the main server thread adds work and from where the 
worker threads pull work*/
struct queue *work_queue; 

/* mutex lock required for the shared queue*/
pthread_mutex_t queue_mutex=PTHREAD_MUTEX_INITIALIZER;

pthread_cond_t queue_cond=PTHREAD_COND_INITIALIZER;
// 线程条件变量

// 创造工作 thread 的 queue，然后init这俩


/*Function to be executed by each worker thread*/
void *handle_conn(void *arg){
	int recvlen;
	int conn_sock;
	char buf[BUFSIZE];
	long long limit;
	long long sum=0,i;

	/* In a loop continue checking if any more work is left to be done*/
	while(1){

		// acquire lock, get connection socket descriptor from work queue, release lock
		// wait if work queue is empty
		pthread_mutex_lock(&queue_mutex);
		while(isempty(work_queue)){  
			pthread_cond_wait(&queue_cond,&queue_mutex);
		}
		conn_sock=work_queue->head->item;
		dequeue(work_queue);
		pthread_mutex_unlock(&queue_mutex);


		// read from connetion socket into buffer
		recvlen = read(conn_sock, &limit, sizeof(limit));

		// print received message		
		if (recvlen > 0) {
			printf("Received number by thread %d: %lld\n", (int)pthread_self(), limit);
		}
		else{
			printf("uh oh - something went wrong!\n");
		}

		sum=0;
		for(i=1;i<=limit;i++){ //compute the sum
			sum+=i;
		}
		// send back to the sender by writing to the conneection socket
		write(conn_sock,&sum,sizeof(sum)); 
	}
	return NULL;
}

int main(int argc, char **argv)
{
	struct sockaddr_in myaddr;	/* our address */
	struct sockaddr_in remaddr;	/* remote address */
    int conn_sock;             /* connection specific socket */
	socklen_t addrlen = sizeof(remaddr);		/* length of addresses */
	int recvlen;			/* # bytes received */
	int servSocket;				/* our socket */
	int msgcnt = 0;			/* count # of messages we received */
	char buf[BUFSIZE];	/* receive buffer */
	int *sock_ptr;
	int i;
	pthread_t tid[NUMTHREADS]; // array to store thread id's
	unsigned short port_num=PORTNO;


	//create work queue
	work_queue=create_queue();


	//create the worker threads
	for(i=0;i<NUMTHREADS;i++){
		pthread_create(&tid[i],NULL,handle_conn,NULL);
	}

	/* create a TCP socket */

	if ((servSocket = socket(AF_INET, SOCK_STREAM, 0)) < 0) {
		printf("Error: cannot create socket\n");
		exit(1);
	}

	/* bind the socket to any valid IP address and a specific port */

	memset((char *)&myaddr, 0, sizeof(myaddr));
	myaddr.sin_family = AF_INET;
	myaddr.sin_addr.s_addr = htonl(INADDR_ANY);
	myaddr.sin_port = htons(port_num);

	if (bind(servSocket, (struct sockaddr *)&myaddr, sizeof(myaddr)) < 0) {
		printf("Error: bind failed\n");
		exit(1);
	}

	// start listening on the created port for incoming connections
	// the second parameter "BACKLOG" specifies the max number of connections that can 
	// wait in a queue to get accepted
	listen(servSocket,BACKLOG);
	printf("waiting on port %d\n",port_num);


	/* now loop, accepting incoming connections and adding them to the work queue */
	while (1){

		// accept incoming connection request and create connection specific socket

        conn_sock=accept(servSocket,(struct sockaddr *)&remaddr, &addrlen);

		// acquire lock, add connection socket to the work queue, 
		// signal the waiting threads, and release lock
		pthread_mutex_lock(&queue_mutex);
		enqueue(work_queue,conn_sock);
		pthread_cond_broadcast(&queue_cond);
		pthread_mutex_unlock(&queue_mutex);
	}
	destroy_queue(work_queue);
	printf("Server program ended normally\n");
    return 0;
}
```



##### pthread_cond_t:

一般pthread_cond_t，会搭配pthread_mutex_t 一起使用的，  因为线程间通信时操作共享内存时，需要用到锁。当锁住的共享变量发生改变时，可能需要通知相应的线程（因为可能该共享变量涉及到多个线程），这时就需要用到pthread_cond_t这种条件变量来精准的通知某个或几个线程， 让他们执行相应的操作

pthread_cond_t涉及两个函数，一个是pthread_cond_signal函数，它在一个线程中，用来发送信号。一个是pthread_cond_wait函数，他在另一个线程中，用来接收信号。当线程执行到pthread_cond_wait函数时，他会释放相应的锁，让其他线程获得锁继续执行，这样其他线程才有机会给他发信号；当它接收到信号时，会重新去获得锁，如果没有获得锁，就阻塞等待，直到获得锁，才执行接收信号的相应操作。

ref:

https://blog.csdn.net/mantis_1984/article/details/78978304
