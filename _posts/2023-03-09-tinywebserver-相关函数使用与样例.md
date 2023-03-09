---
layout: post
title: TinyWebServer 相关函数使用与样例 [定时器]
date: 2023-03-09 19:13 +0800
categories: [C++, 项目]
tags: [TinyWebServer, SIGALRM信号]
---

Linux 提供了三种定时的方法：

- socket 选项 SO_RECVTIMEO 和 SO_SNDTIMEO
- SIGALRM 信号
- I/O 复用系统调用的超时参数

三种方法没有一劳永逸的应用场景，也没有绝对的优劣。TinyWebServer 中用的 SIGALRM 信号，利用 `alrnm` 函数周期性地出发 `SIGALRM` 信号，信号处理函数利用管道通知主循环，主循环接收到该信号后对升序链表上所有定时器进行处理，若该段事件内没有交换数据，则将该连接关闭，释放所占用的资源



**sigaction 结构体**

```c++
struct sigaction {
    void (*sa_handler)(int);
    void (*sa_sigaction)(int, siginfo_t *, void *);
    sigset_t sa_mask;
    int sa_flags;
    void (*sa_restorer)(void);
}
```

- sa_handler 是一个函数指针，指向信号处理函数

- sa_sigaction 同样是信号处理函数，有三个参数，可以获得关于信号更详细的信息

- sa_mask 用来指定在信号处理函数执行期间需要被屏蔽的信号

- sa_flags 用于指定信号处理的行为

- - SA_RESTART，使被信号打断的系统调用自动重新发起
  - SA_NOCLDSTOP，使父进程在它的子进程暂停或继续运行时不会收到 SIGCHLD 信号
  - SA_NOCLDWAIT，使父进程在它的子进程退出时不会收到 SIGCHLD 信号，这时子进程如果退出也不会成为僵尸进程
  - SA_NODEFER，使对信号的屏蔽无效，即在信号处理函数执行期间仍能发出这个信号
  - SA_RESETHAND，信号处理之后重新设置为默认的处理方式
  - SA_SIGINFO，使用 sa_sigaction 成员而不是 sa_handler 作为信号处理函数

- sa_restorer 一般不使用



**sigaction 函数**

```c++
#include <signal.h>

int sigaction(int signum, const struct sigaction *act, struct sigaction *oldact);
```

- signum 表示操作的信号。
- act 表示对信号设置新的处理方式。
- oldact 表示信号原来的处理方式。
- 返回值，0 表示成功，-1 表示有错误发生



**sigfillset 函数**

```c++
#include <signal.h>

int sigfillset(sigset_t *set);
```

用来将参数 set 信号集初始化，然后把所有的信号加入到此信号集



**SIGALRM、SIGTERM 信号**

```c++
#define SIGALRM  14     //由alarm系统调用产生timer时钟信号
#define SIGTERM  15     //终端发送的终止信号
```



alrm 函数

```c++
#include <unistd.h>;

unsigned int alarm(unsigned int seconds);

```

设置信号传送闹钟，即用来设置信号 SIGALRM 在经过参数 seconds 秒数后发送给目前的进程。如果未设置信号 SIGALRM 的处理函数，那么 alarm() 默认处理终止进程



**socketpair 函数**

在 linux 下，使用 socketpair 函数能够创建一对套接字进行通信，`TinyWebServer` 中使用管道通信

```c++
#include <sys/types.h>
#include <sys/socket.h>

int socketpair(int domain, int type, int protocol, int sv[2]);
```

- domain 表示协议族，PF_UNIX 或者 AF_UNIX
- type 表示协议，可以是 SOCK_STREAM 或者 SOCK_DGRAM ，SOCK_STREAM 基于 TCP ，SOCK_DGRAM 基于 UDP
- protocol 表示类型，只能为 0
- sv[2] 表示套节字柄对，该两个句柄作用相同，均能进行读写双向操作
- 返回结果， 0 为创建成功，-1 为创建失败



**send 函数**

```c++
#include <sys/types.h>
#include <sys/socket.h>

ssize_t send(int sockfd, const void *buf, size_t len, int flags);
```

当套接字发送缓冲区变满时，send 通常会阻塞，除非套接字设置为非阻塞模式，当缓冲区变满时，返回 EAGAIN 或者 EWOULDBLOCK 错误，此时可以调用 select 函数来监视何时可以发送数据
