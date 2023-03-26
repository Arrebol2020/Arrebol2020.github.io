---
layout: post
title: Linux 的 I/O 多路复用机制
date: 2023-03-19 16:34 +0800
categories:
- 学习随笔
- Linux
tags:
- Linux
- I/O 复用
---



Linux 的 IO 多路复用是一种机制，可以让单个进程监视多个文件描述符，一旦某个描述符就绪（一般是读就绪或写就绪），能够通知程序进行相应的读写操作。Linnux 提供了三种 IO 多路复用的方式：select、poll 和 epoll。它们的功能是类似的，但具体细节各有不同。

IO 多路复用的应用场景很多，主要是`在需要处理大量并发 IO 请求的情况下`，例如网络服务器、聊天室、代理服务器等。IO 多路复用可以让一个线程监视多个文件描述符，而不需要为每个连接创建一个线程，从而节省资源和提高效率。一些常见的使用 IO 多路复用的技术或框架有 Java NIO、Redis、Nginx 和 Netty 等。

​    

> I/O 多路复用和异步 I/O
{: .prompt-tip}

IO 多路复用和异步 IO 的关系是，它们都可以让程序不阻塞于某个特定的 IO 系统调用，但它们的实现方式不同。IO 多路复用是一种同步 IO，因为它只是在等待 IO 事件的时候不阻塞，但当 IO 事件发生时，还是需要程序进行读写操作。异步 IO 是一种真正的异步 IO，因为它会为 IO 事件绑定处理程序，当事件发生时触发处理程序，而不需要程序主动去读写数据。  

​     

## select

select 函数是一个用于监视多个文件描述符（通常是套接字）的状态（可读，可写，异常）的函数。它的原型如下：

```c++
int select(int nfds, fd_set *readfds, fd_set *writefds,
           fd_set *exceptfds, struct timeval *timeout);
```

其中，nfds 是要监视的文件描述符的最大值加一；readfds 是一个指向可读文件描述符集合的指针；writefds 是一个指向可写文件描述符集合的指针；exceptfds 是一个指向异常文件描述符集合的指针；timeout 是一个指向超时时间结构体的指针。

select 函数返回时，会修改 readfds，writefds 和 exceptfds 参数，以反映哪些文件描述符已经准备好进行相应的操作。如果没有任何文件描述符准备好，在超时时间到达之前，select函数会阻塞等待。如果超时时间为 NULL，select 函数会无限期地等待。

select 函数返回值有三种情况：

- 如果有至少一个文件描述符准备好了，返回准备好的文件描述符的数量。
- 如果在超时时间到达之前没有任何文件描述符准备好了，返回0。
- 如果发生错误，返回 -1，并设置 errno 变量。

​    

简单样例：

```c++
#include <sys/time.h>
#include <sys/types.h>
#include <unistd.h>
#include <iostream>

# define STDIN 0  // file descriptor for standard input

int main()
{
    struct timeval tv;  // a structure to store the timeout value
    fd_set readfds;  // a set of file descripoors to monitor for reading

    tv.tv_sec = 2;  // set the timeout to 2 seconds
    tv.tv_usec = 500000;  // and 500 milliseconds

    FD_ZERO(&readfds);  // clear the set of file descriptors
    FD_SET(STDIN, &readfds);  // add the standard input to the set

    // don't care about writefds and exceptfds
    select(STDIN+1, &readfds, NULL, NULL, &tv);
    // wait until either the standard input is ready for reading or the timeout expires

    if(FD_ISSET(STDIN, &readfds))  // check if the standard input is in the set
        std::cout << "A key was pressed!\n" << std::endl;
    else
        std::cout << "Timed out.\n" << std::endl;
    
    return 0;
}
```

> 这段代码的输出取决于用户是否在超时之前按下了任何键。如果用户按下了键，输出将是`A key was pressed!`，否则输出将是`Timed out.`。

​    

## poll

poll 函数是 Linux 中的字符设备驱动中的一个函数，用于监视文件描述符的状态，当文件描述符就绪时，poll 函数会通知程序进行相应的读写操作。

poll  函数与select 函数类似，都是用于 I/O 多路复用的。但是 poll 函数没有最大文件描述符数量的限制，而且 poll 函数使用链表来存储文件描述符，所以 poll 函数可以处理任意数量的文件描述符。另外，poll 函数还支持更多的事件类型。

函数定义：

```c++
int poll(struct pollfd *fds, nfds_t nfds, int timeout);
```

参数：

- `fds`：指向一个 `pollfd` 结构体数组的指针，每个结构体包含文件描述符以及待监视的事件。
- `nfds`：`fds` 数组中的元素个数。
- `timeout`：超时值，单位为毫秒。若设置为 `-1`，则表示等待直到有事件发生。

`pollfd` 结构体的定义如下：

```c++
struct pollfd {
    int fd;         // 文件描述符
    short events;   // 等待的事件
    short revents;  // 实际发生的事件
};
```

其中 `events` 为等待的事件，可选值为以下之一：

- `POLLIN`：有数据可读
- `POLLOUT`：可以写入数据
- `POLLPRI`：有紧急数据可读
- `POLLERR`：发生错误
- `POLLHUP`：对端关闭连接
- `POLLNVAL`：文件描述符未打开或已关闭

头文件：

```c++
#include <poll.h>
```

函数作用：

`poll` 函数用于等待一组文件描述符中的任何一个发生指定的事件。

函数返回值：函数返回值为发生事件的文件描述符个数，或者以下三种情况之一：

- `-1`：发生错误，错误码存储在 `errno` 中。
- `0`：超时，没有文件描述符发生事件。
- `EINTR`：等待过程中被信号中断。

以下是一个使用poll函数的C++样例代码，该代码使用poll函数实现了一个简单的TCP C/S模型。

```c++
#include <iostream>
#include <cstring>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <unistd.h>
#include <sys/poll.h>

using namespace std;

int main()
{
    // 创建监听 socket
    int listen_fd = socket(AF_INET, SOCK_STREAM, 0);
    if (listen_fd == -1)
    {
        cout << "socket error" << endl;
        return -1;
    }

    // 绑定地址和端口
    struct sockaddr_in server_addr;
    memset(&server_addr, 0, sizeof(server_addr));
    server_addr.sin_family = AF_INET;
    server_addr.sin_port = htons(12345);  // 绑定端口为 12345
    server_addr.sin_addr.s_addr = htonl(INADDR_ANY);  // 绑定所有地址

    if (bind(listen_fd, (struct sockaddr*)&server_addr, sizeof(server_addr)) == -1)
    {
        cout << "bind error" << endl;
        return -1;
    }
    
    // 监听 socket
    if (listen(listen_fd, 5) == -1)
    {
        cout << "listen error" << endl;
        return -1;
    }

    // 初始化 pollfd 数组
    struct pollfd fds[1024];
    fds[0].fd = listen_fd;
    fds[0].events = POLLIN;

    int max_fd = 0;

    while (true)
    {
        // 调用 poll 等待事件
        int ret = poll(fds, max_fd + 1, -1);
        if (ret == -1)
        {
            cout << "poll error" << endl;
            break;
        }

        // 处理已就绪的文件描述符
        for (int i = 0; i <= max_fd; ++i)
        {
            if (fds[i].revents & POLLIN)  // 可读事件
            {
                if (fds[i].fd == listen_fd)  // 新连接事件
                {
                    struct sockaddr_in client_addr;
                    socklen_t client_len = sizeof(client_addr);
                    int client_fd = accept(listen_fd, (struct sockaddr*)&client_addr, &client_len);
                    if (client_fd == -1)
                    {
                        cout << "accept error" << endl;
                        continue;
                    }

                    fds[++max_fd].fd = client_fd;
                    fds[max_fd].events = POLLIN;

                    cout << "new connection from " << inet_ntoa(client_addr.sin_addr) << ":" << ntohs(client_addr.sin_port) << ", fd: " << client_fd << endl;
                }
                else
                {
                    char buf[1024];
                    memset(buf, 0, sizeof(buf));
                    int len = recv(fds[i].fd, buf, sizeof(buf), 0);
                    if (len <= 0)
                    {
                        close(fds[i].fd);
                        fds[i].fd = -1;
                        continue;
                    }

                    cout << "recv from fd: " << fds[i].fd << ", content: " << buf;

                    send(fds[i].fd, buf, len, 0);
                }
            }
        }

        for (int i = max_fd; i >= 0; --i)
        {
            if (fds[i].fd == -1)
            {
                swap(fds[i], fds[max_fd--]);
            }
        }
    }

    close(listen_fd);

    return 0;
}
```

   

## epoll

[epoll 介绍](../linux-epoll)

   

## 写在最后

感谢你在茫茫人海中找到我🕵🏼

<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>

<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.3.1/css/all.css" integrity="sha384-mzrmE5qonljUremFsqc01SB46JvROS7bZs3IO2EmfFsd15uHvIt+Y8vEf7N7fWAU" crossorigin="anonymous">

<span id="busuanzi_container_page_pv">🎉你是第 <span id="busuanzi_value_page_pv"><i class="fa fa-spinner fa-spin"></i>  </span> 个读者

㊗️ 你平安喜乐，顺遂无忧！

希望你读完有所收获～

🥂🥂🥂 
