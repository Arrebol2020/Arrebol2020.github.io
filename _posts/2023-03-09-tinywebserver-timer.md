---
layout: post
title: TinyWebServer 相关函数使用与样例 [定时器]
date: 2023-03-09 19:13 +0800
categories:
- 项目
- TinyWebServer
tags:
- C++
---
Linux 提供了三种定时的方法：

- socket 选项 SO_RECVTIMEO 和 SO_SNDTIMEO
- SIGALRM 信号
- I/O 复用系统调用的超时参数

三种方法没有一劳永逸的应用场景，也没有绝对的优劣。TinyWebServer 中用的 SIGALRM 信号，利用 `alrnm` 函数周期性地出发 `SIGALRM` 信号，信号处理函数利用管道通知主循环，主循环接收到该信号后对升序链表上所有定时器进行处理，若该段事件内没有交换数据，则将该连接关闭，释放所占用的资源

   

## sigaction 结构体

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

   

## sigaction 函数

`sigaction()` 函数用于安装信号处理程序。它的定义如下：

```c++
int sigaction(int signum, const struct sigaction *act, struct sigaction *oldact);
```

该函数的三个参数如下：

- `signum`：要处理的信号的编号。
- `act`：一个指向 `sigaction` 结构体的指针，它指定了新的信号处理程序。如果该指针为 `NULL`，则表示忽略该信号。
- `oldact`：一个指向 `sigaction` 结构体的指针，它用于存储旧的信号处理程序。如果该指针不为 `NULL`，则 `sigaction()` 函数将在调用时将旧的信号处理程序复制到该结构体中。

`sigaction()` 函数返回以下三种值之一：

- 如果成功，返回 `0`。
- 如果发生错误，返回 `-1` 并设置 `errno` 变量来指示错误的原因。
- 如果信号处理程序不能被安装，返回 `1`。

例如，下面的代码将为 `SIGINT` 信号安装一个新的信号处理程序，并将旧的信号处理程序存储在 `oldact` 变量中：

```c++
#include <iostream>
#include <csignal>

void my_handler(int signum)
{
    std::cout << "Received signal " << signum << std::endl;
}


int main()
{
    struct sigaction newact, oldact;
    
    newact.sa_handler = my_handler;
    sigemptyset(&newact.sa_mask);  // 清空信号集
    newact.sa_flags = 0;

    // 安装新的信号处理程序，保存旧的信号处理程序
    if(sigaction(SIGINT, &newact, &oldact) != 0)
    {
        std::cerr << "Error installing signal handler" << std::endl;
        return 1;
    }

    std::cout << "Singal handler installed" << std::endl;
    
    // 在这里进行一些工作...

    // 恢复旧的信号处理程序
    if (sigaction(SIGINT, &oldact, NULL) != 0) {
        std::cerr << "Error restoring signal handler" << std::endl;
        return 1;
    }

    std::cout << "Signal handler restored" << std::endl;

    return 0;
}
```

这段代码演示了如何使用 `sigaction()` 函数安装和恢复信号处理程序，以及如何处理信号。

   

## sigfillset 函数

`sigfillset()` 函数用于将所有信号添加到一个信号集中。其定义如下：

```c++
int sigfillset(sigset_t *set);
```

该函数接受一个指向 `sigset_t` 类型的指针 `set`，并将所有信号添加到该信号集中。如果成功，则返回 0，否则返回 -1 并设置 `errno` 变量来指示错误的原因。

以下是一个示例程序，演示如何使用 `sigfillset()` 函数将所有信号添加到信号集中：

```c++
#include <iostream>
#include <csignal>

int main() {
  sigset_t set;

  if (sigfillset(&set) != 0) {
    std::cerr << "Error creating signal set" << std::endl;
    return 1;
  }

  std::cout << "All signals added to signal set" << std::endl;

  return 0;
}
```

在这个示例程序中，我们首先定义了一个 `sigset_t` 类型的变量 `set`，它用于存储信号集。然后，我们调用 `sigfillset()` 函数将所有信号添加到 `set` 变量中。如果函数返回错误，我们输出错误消息并返回 1。否则，我们输出成功消息并终止程序。

> 需要注意的是，`sigfillset()` 函数将所有信号添加到信号集中，包括那些在当前平台上可能不支持或未定义的信号。因此，在使用 `sigfillset()` 函数时，应谨慎考虑。通常情况下，我们只需要将一组特定的信号添加到信号集中，而不是添加所有信号。可以使用 `sigaddset()` 函数将单个信号添加到信号集中。
{: .prompt-tip}

   

## SIGALRM、SIGTERM 信号

```c++
#define SIGALRM  14     //由alarm系统调用产生timer时钟信号
#define SIGTERM  15     //终端发送的终止信号
```



## alrm 函数

`alarm()` 函数用于安排一个定时器，在指定的时间后向进程发送 `SIGALRM` 信号。其定义如下：

```c++
unsigned int alarm(unsigned int seconds);
```

该函数接受一个整数参数 `seconds`，指定定时器在多少秒后到期。如果以前已经设置了定时器，则返回以前定时器剩余的秒数。如果未设置定时器，则返回 0。

以下是一个示例程序，演示如何使用 `alarm()` 函数设置一个定时器：

```c++
#include <iostream>
#include <csignal>
#include <unistd.h>

void my_handler(int signum)
{
    std::cout << "Received signal " << signum << std::endl;
}

int main()
{
    struct sigaction sa;
    sa.sa_handler = my_handler;
    sigaction(SIGALRM, &sa, nullptr);
    //signal(SIGINT, my_handler);  // 设置信号处理程序
    
    int seconds = 5;

    unsigned int remaining = alarm(seconds); // 设置一个 5 秒的定时器

    std::cout << "Timer set for " << seconds << " seconds" << std::endl;

    sleep(10);

    return 0;
}
```

程序输出：

```bash
Timer set for 5 seconds
Received signal 14
```

> 需要注意的是，`alarm()` 函数只允许为进程安排一个定时器，因此如果程序需要多个定时器，则需要使用其他方法，如使用 `setitimer()` 函数。此外，`alarm()` 函数使用全局定时器，因此如果在程序中同时使用多个 `alarm()` 函数，则可能会导致不可预测的结果。

   

## socketpair 函数

`socketpair()` 函数是在 Linux 和其他类 Unix 操作系统上可用的一种 IPC（进程间通信）机制，用于创建一对相互连接的套接字。这对套接字可以用于在同一台计算机上的两个进程之间进行双向通信。

`socketpair()` 函数的语法如下：

```c++
int socketpair(int domain, int type, int protocol, int sv[2]);
```

其中，

- `domain` 参数指定套接字的协议族，通常使用 `AF_UNIX` 表示在同一台计算机上的进程之间进行通信。
- `type` 参数指定套接字的类型，通常使用 `SOCK_STREAM` 或 `SOCK_DGRAM`。
- `protocol` 参数指定套接字的协议，通常为 0。
- `sv` 参数是一个由两个整数组成的数组，用于保存创建的一对套接字的文件描述符。

调用 `socketpair()` 函数时，如果成功创建了一对套接字，则返回 0，并将创建的套接字的文件描述符保存在 `sv` 数组中。如果出现错误，则返回 -1，并设置相应的错误码。

例如，以下代码片段展示了如何使用 `socketpair()` 函数创建一对套接字：

```c++
#include <sys/socket.h>
#include <unistd.h>

int main() {
    int sv[2];
    if (socketpair(AF_UNIX, SOCK_STREAM, 0, sv) == -1) {
        perror("socketpair");
        return 1;
    }
    // 使用 sv[0] 和 sv[1] 进行通信
    // ...
    close(sv[0]);
    close(sv[1]);
    return 0;
}
```

在这个示例中，我们使用 `socketpair()` 函数创建了一对相互连接的套接字，并将文件描述符保存在 `sv` 数组中。然后，我们可以使用 `sv[0]` 和 `sv[1]` 这两个文件描述符进行通信。最后，我们需要使用 `close()` 函数关闭这对套接字，以释放系统资源。

> 请注意，`socketpair()` 函数只能在同一台计算机上的进程之间进行通信，如果需要在不同的计算机上进行通信，则需要使用网络套接字，例如 TCP 或 UDP 套接字。
{: .prompt-tip}

   

## send 函数

当套接字发送缓冲区变满时，send 通常会阻塞，除非套接字设置为非阻塞模式，当缓冲区变满时，返回 EAGAIN 或者 EWOULDBLOCK 错误，此时可以调用 select 函数来监视何时可以发送数据。

`send()` 函数是在 Linux 和其他类 Unix 操作系统上可用的一种系统调用，用于向已连接的套接字发送数据。该函数的原型如下：

```c++
ssize_t send(int sockfd, const void *buf, size_t len, int flags);
```

其中，

- `sockfd` 参数是已连接套接字的文件描述符。
- `buf` 参数是指向要发送数据的缓冲区的指针。
- `len` 参数是要发送的数据的字节数。
- `flags` 参数是可选的标志参数，通常设置为 0。

`send()` 函数返回值是发送的字节数，如果发生错误，则返回 -1，并设置相应的错误码。

例如，以下代码片段展示了如何使用 `send()` 函数向已连接的套接字发送数据：

```c++
#include <sys/socket.h>
#include <unistd.h>

int main() {
    int sockfd = socket(AF_UNIX, SOCK_STREAM, 0);
    // 连接到远程主机
    // ...
    const char *msg = "Hello, world!";
    ssize_t num_bytes_sent = send(sockfd, msg, strlen(msg), 0);
    if (num_bytes_sent == -1) {
        perror("send");
        return 1;
    }
    close(sockfd);
    return 0;
}
```

在这个示例中，我们首先使用 `socket()` 函数创建了一个套接字，然后连接到远程主机。然后，我们定义了要发送的数据的字符串 `msg`，并使用 `send()` 函数将其发送到已连接的套接字中。如果成功发送了数据，则 `send()` 函数返回发送的字节数，否则返回 -1。最后，我们使用 `close()` 函数关闭套接字，以释放系统资源。

请注意，`send()` 函数只能用于已连接的套接字。如果需要向未连接的套接字发送数据，则需要使用 `sendto()` 函数。此外，如果要发送的数据量很大，则可能需要多次调用 `send()` 函数才能将所有数据发送完毕。

   

## 参考

- 最新版Web服务器项目详解 - 07 定时器处理非活动连接（上）：https://mp.weixin.qq.com/s/mmXLqh_NywhBXJvI45hchA
- 最新版Web服务器项目详解 - 08 定时器处理非活动连接（下）：https://mp.weixin.qq.com/s/fb_OUnlV1SGuOUdrGrzVgg



## 写在最后

感谢你在茫茫人海中找到我🕵🏼

<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>

<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.3.1/css/all.css" integrity="sha384-mzrmE5qonljUremFsqc01SB46JvROS7bZs3IO2EmfFsd15uHvIt+Y8vEf7N7fWAU" crossorigin="anonymous">

<span id="busuanzi_container_page_pv">🎉你是第 <span id="busuanzi_value_page_pv"><i class="fa fa-spinner fa-spin"></i>  </span> 个读者

㊗️ 你平安喜乐，顺遂无忧！

希望你读完有所收获～

🥂🥂🥂 
