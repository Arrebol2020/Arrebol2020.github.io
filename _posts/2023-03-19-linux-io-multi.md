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



## 写在最后

感谢你在茫茫人海中找到我🕵🏼

<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>

<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.3.1/css/all.css" integrity="sha384-mzrmE5qonljUremFsqc01SB46JvROS7bZs3IO2EmfFsd15uHvIt+Y8vEf7N7fWAU" crossorigin="anonymous">

<span id="busuanzi_container_page_pv">🎉你是第 <span id="busuanzi_value_page_pv"><i class="fa fa-spinner fa-spin"></i>  </span> 个读者

㊗️ 你平安喜乐，顺遂无忧！

希望你读完有所收获～

🥂🥂🥂 
