---
layout: post
title: TinyWebServer 相关函数使用与样例 [http连接处理]
date: 2023-03-03 17:11 +0800
categories:
- 项目
- TinyWebServer
tags:
- C++
- http
---



## epoll_create 函数

函数定义：

```c++
int epoll_create(int size);
```

参数：

- `size`：epoll 实例能够监听的文件描述符数量的上限。实际上这个值并不是硬限制，内核会为 epoll 实例动态分配内存以适应更多的文件描述符。但 `size` 参数可以影响 epoll 实例分配内存的方式。

头文件：

```c++
#include <sys/epoll.h>
```

返回值：

- 如果成功，返回 epoll 实例的文件描述符。该描述符可以用于 `epoll_ctl()` 和 `epoll_wait()` 函数。
- 如果失败，返回 -1，并设置错误码（errno）。



示例：

```c++
#include <sys/epoll.h>
#include <unistd.h>
#include <iostream>

int main() {
    // 创建 epoll 实例
    int epfd = epoll_create(10);
    if (epfd < 0) {
        std::cerr << "Failed to create epoll instance!" << std::endl;
        return 1;
    }
    std::cout << "Epoll instance created, fd = " << epfd << std::endl;

    // 关闭 epoll 实例
    close(epfd);

    return 0;
}
```

该示例中，首先通过 `epoll_create()` 函数创建一个 epoll 实例，指定了能够监听的文件描述符数量的上限为 10。如果成功创建，函数返回实例的文件描述符，否则返回 -1，并设置错误码。如果创建成功，可以将该描述符用于后续的 `epoll_ctl()` 和 `epoll_wait()` 操作。最后通过 `close()` 函数关闭 epoll 实例。



## epoll_ctl 函数

``epoll_ctl` 是 Linux 中使用 epoll I/O 模型时的一个函数，它用于向 epoll 实例中添加、修改或删除文件描述符。

函数定义：

```c++
int epoll_ctl(int epfd, int op, int fd, struct epoll_event* event);
```

参数说明：

- `epfd`：epoll 实例的文件描述符·

- `op`：要进行的操作，包括：

  - `EPOLL_CTL_ADD`：向 epoll 实例中添加一个文件描述符
  - `EPOLL_CTL_MOD`：修改 epoll 实例中已有的文件描述符
  - `EPOLL_CTL_DEL`：从 epoll 实例中删除一个文件描述符

- `fd`：要添加、修改或删除的文件描述符

- `event`：对应的事件，为 `struct epoll_event` 类型的指针

  - `event`是 epoll_event 结构体指针类型，表示内核所监听的事件，具体定义如下：

    ```c++
    struct epoll_event {
    __uint32_t events; /* Epoll events */
    epoll_data_t data; /* User data variable */
    };
    ```

    events 描述事件类型，其中 epoll 事件类型有以下几种：

    - EPOLLIN：表示对应的文件描述符可以读（包括对端SOCKET正常关闭）
    - EPOLLOUT：表示对应的文件描述符可以写
    - EPOLLPRI：表示对应的文件描述符有紧急的数据可读（这里应该表示有带外数据到来）
    - EPOLLERR：表示对应的文件描述符发生错误
    - EPOLLHUP：表示对应的文件描述符被挂断；
    - EPOLLET：将EPOLL设为边缘触发(Edge Triggered)模式，这是相对于水平触发(Level Triggered)而言的
    - EPOLLONESHOT：只监听一次事件，当监听完这次事件之后，如果还需要继续监听这个socket的话，需要再次把这个socket加入到EPOLL队列里

函数返回值：

- 成功返回0，失败返回-1，错误码保存在 errno 中。

下面是一个简单的使用 `epoll_ctl` 的示例：

```c++
#include <sys/epoll.h>
#include <fcntl.h>
#include <unistd.h>
#include <iostream>
#include <cstring>

int main() {
    int epfd = epoll_create(10); // 创建 epoll 实例
    if (epfd < 0) {
        std::cerr << "Failed to create epoll instance: " << std::strerror(errno) << std::endl;
        return -1;
    }

    int fd = open("test.txt", O_RDWR); // 打开文件
    if (fd < 0) {
        std::cerr << "Failed to open file: " << std::strerror(errno) << std::endl;
        return -1;
    }

    struct epoll_event ev;
    ev.data.fd = fd;
    ev.events = EPOLLIN | EPOLLOUT | EPOLLET;

    // 添加文件描述符到 epoll 实例中
    if (epoll_ctl(epfd, EPOLL_CTL_ADD, fd, &ev) < 0) {
        std::cerr << "Failed to add fd to epoll instance: " << std::strerror(errno) << std::endl;
        return -1;
    }

    close(epfd);
    close(fd);
    return 0;
}
```

上面的示例中，首先创建了一个 epoll 实例，然后打开一个文件，接着将该文件的文件描述符添加到 epoll 实例中，最后关闭文件描述符和 epoll 实例。



## epoll_wait 函数

`epoll_wait` 函数用于等待已注册的文件描述符上的事件，它的函数定义如下：

```c++
int epoll_wait(int epfd, struct epoll_event *events, int maxevents, int timeout);
```

其中参数含义如下：

- `epfd`: epoll实例的文件描述符。
- `events`: 存储事件的结构体指针，是一个数组，其大小应该为 `maxevents`。
- `maxevents`: `events` 数组的大小，表示期望的最大事件数量。
- `timeout`: 超时时间，单位为毫秒。如果为负数，则表示一直等待，如果为0，则表示立即返回，如果大于0，则表示等待的最长时间。

函数返回值为已就绪的事件数量，如果返回值为0，则表示已超时。函数执行出错时，返回-1，并设置errno。

简单的使用示例：

```c++
#include <sys/epoll.h>
#include <unistd.h>
#include <iostream>

#define MAX_EVENTS 10

int main() {
    int epfd = epoll_create(1);
    if (epfd == -1) {
        perror("epoll_create error");
        return -1;
    }

    struct epoll_event ev, events[MAX_EVENTS];
    ev.data.fd = STDIN_FILENO;
    ev.events = EPOLLIN;

    if (epoll_ctl(epfd, EPOLL_CTL_ADD, STDIN_FILENO, &ev) == -1) {
        perror("epoll_ctl error");
        close(epfd);
        return -1;
    }

    while (true) {
        int nfds = epoll_wait(epfd, events, MAX_EVENTS, -1);
        if (nfds == -1) {
            perror("epoll_wait error");
            break;
        }

        for (int i = 0; i < nfds; i++) {
            if (events[i].data.fd == STDIN_FILENO) {
                char buf[1024];
                int n = read(STDIN_FILENO, buf, sizeof(buf));
                buf[n] = '\0';
                std::cout << "Read " << n << " bytes from stdin: " << buf;
            }
        }
    }

    close(epfd);
    return 0;
}
```

该示例使用epoll来等待标准输入上的事件，当输入有数据到达时，程序将读取并输出到控制台。



## select/poll/epoll

select、poll、epoll 都是 Linux 下的 I/O 多路复用机制，用于处理多个文件描述符（socket 文件句柄）的 I/O 事件。

其中 select 是最早的 I/O 多路复用机制，但它有一个问题是单个进程所能打开的文件描述符有限，因为它是基于数组实现的，文件描述符的数量大小由 FD_SETSIZE 决定，默认是 1024，可通过重新编译内核来修改。

为了解决这个问题，poll 应运而生。poll 没有文件描述符数量的限制，原理与 select 类似，但是它不是基于数组实现的，而是基于链表实现的。

select 和 poll 通过将所有文件描述符拷贝到内核态，每次调用都需要拷贝。epoll 通过 epoll_create 建立一棵红黑树，通过 epoll_ctl 将要监听的文件描述符注册到红黑树上

epoll 是 Linux 下的新一代 I/O 多路复用机制，它的设计思想与 poll 相似，但更加高效，具体体现在以下几个方面：

- 支持一个进程打开大量的文件描述符（默认最大可以打开 1000 万个描述符，可以通过修改系统参数进行调整）。

- 相比较于 select 和 poll，采用了事件通知的机制，当没有事件发生时，epoll 是不会产生返回值的，避免了轮询的过程。

- 采用了红黑树的数据结构来存储文件描述符，能够在添加和删除事件时提供 O(log n) 的时间复杂度，同时也可以使用 EPOLLET 模式，以避免因为某个文件描述符上的事件未处理而导致的频繁触发 epoll_wait() 函数调用。

总的来说，epoll 比 select 和 poll 更加高效，尤其是在高并发的网络编程中。但是它也有一些缺点，比如对代码的复杂度要求比较高，不易于理解和调试。



详细区别：

- 调用函数

- - select 和 poll 都是一个函数，epoll 是一组函数

- 文件描述符数量

- - select 通过线性表描述文件描述符集合，文件描述符有上限，一般是 1024，但可以修改源码，重新编译内核，不推荐
  - poll 是链表描述，突破了文件描述符上限，最大可以打开文件的数目
  - epoll 通过红黑树描述，最大可以打开文件的数目，可以通过命令 ulimit -n number 修改，仅对当前终端有效

- 将文件描述符从用户传给内核

- - select 和 poll 通过将所有文件描述符拷贝到内核态，每次调用都需要拷贝
  - epoll 通过 epoll_create 建立一棵红黑树，通过 epoll_ctl 将要监听的文件描述符注册到红黑树上

- 内核判断就绪的文件描述符

- - select 和 poll 通过遍历文件描述符集合，判断哪个文件描述符上有事件发生
  - epoll_create 时，内核除了帮我们在epoll文件系统里建了个红黑树用于存储以后 epoll_ctl 传来的 fd 外，还会再建立一个 list 链表，用于存储准备就绪的事件，当 epoll_wait 调用时，仅仅观察这个 list 链表里有没有数据即可。
  - epoll 是根据每个 fd 上面的回调函数(中断函数)判断，只有发生了事件的 socket 才会主动的去调用 callback函数，其他空闲状态 socket 则不会，若是就绪事件，插入 list

- 应用程序索引就绪文件描述符

- - select/poll 只返回发生了事件的文件描述符的个数，若知道是哪个发生了事件，同样需要遍历
  - epoll 返回的发生了事件的个数和结构体数组，结构体包含 socket 的信息，因此直接处理返回的数组即可

- 工作模式

- - select 和 poll 都只能工作在相对低效的 LT 模式下
  - epoll 则可以工作在 ET 高效模式，并且 epoll 还支持 EPOLLONESHOT 事件，该事件能进一步减少可读、可写和异常事件被触发的次数。 

- 应用场景

- - 当所有的 fd 都是活跃连接，使用 epoll，需要建立文件系统，红黑树和链表对于此来说，效率反而不高，不如selece和poll
  - 当监测的 fd 数目较小，且各个 fd 都比较活跃，建议使用 select 或者 poll
  - 当监测的 fd 数目非常大，成千上万，且单位时间只有其中的一部分 fd 处于就绪状态，这个时候使用 epoll 能够明显提升性能



## ET 和 LT

ET和LT是epoll中的两种工作方式，指定事件触发的模式。

- LT（Level Triggered）：当一个事件被触发后，如果没有被处理，下次 epoll_wait 仍然会通知你，直到这个事件被处理。
- ET（Edge Triggered）：当一个事件被触发后，如果没有被处理，下次 epoll_wait 不会再通知你，直到新的事件到来。

ET是 epoll 的高效工作方式，它只通知发生变化的文件描述符，不会重复通知已经处理过的文件描述符，从而减少了不必要的系统调用，提高了效率。但是 ET 需要保证读写缓冲区被一次性处理完整，否则会有遗漏，出现问题。因此，使用ET时需要注意缓冲区的处理，必须要一次性将数据读取完，使用非阻塞 I/O，读取到出现 eagain。

- 

## EPOLLONESHOT

- 我们期望的是一个socket连接在任一时刻都只被一个线程处理，通过epoll_ctl对该文件描述符注册epolloneshot事件，一个线程处理socket时，其他线程将无法处理，**当该线程处理完后，需要通过epoll_ctl重置epolloneshot事件**

- 一个线程读取某个socket上的数据后开始处理数据，在处理过程中该socket上又有新数据可读，此时另一个线程被唤醒读取，此时出现两个线程处理同一个socket



## HTTP报文格式

HTTP报文分为请求报文和响应报文两种，每种报文必须按照特有格式生成，才能被浏览器端识别。

其中，浏览器端向服务器发送的为请求报文，服务器处理后返回给浏览器端的为响应报文。

### **请求报文**

HTTP请求报文由请求行（request line）、请求头部（header）、空行和请求数据四个部分组成。

其中，请求分为两种，GET和POST，具体的：

- **GET**

```bash
    GET /562f25980001b1b106000338.jpg HTTP/1.1
    Host:img.mukewang.com
    User-Agent:Mozilla/5.0 (Windows NT 10.0; WOW64)
    AppleWebKit/537.36 (KHTML, like Gecko) Chrome/51.0.2704.106 Safari/537.36
    Accept:image/webp,image/*,*/*;q=0.8
    Referer:http://www.imooc.com/
    Accept-Encoding:gzip, deflate, sdch
    Accept-Language:zh-CN,zh;q=0.8
    空行
    请求数据为空
```



- **POST**

```bash
    POST / HTTP1.1
    Host:www.wrox.com
    User-Agent:Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; .NET CLR .0.50727; .NET CLR 3.0.04506.648; .NET CLR 3.5.21022)
    Content-Type:application/x-www-form-urlencoded
    Content-Length:40
    Connection: Keep-Alive
    空行
    name=Professional%20Ajax&publisher=Wiley
```

> - **请求行**，用来说明请求类型,要访问的资源以及所使用的HTTP版本。
>   GET说明请求类型为GET，/562f25980001b1b106000338.jpg(URL)为要访问的资源，该行的最后一部分说明使用的是HTTP1.1版本。
>
> - **请求头部**，紧接着请求行（即第一行）之后的部分，用来说明服务器要使用的附加信息。
>
> - - HOST，给出请求资源所在服务器的域名。
>   - User-Agent，HTTP客户端程序的信息，该信息由你发出请求使用的浏览器来定义,并且在每个请求中自动发送等。
>   - Accept，说明用户代理可处理的媒体类型。
>   - Accept-Encoding，说明用户代理支持的内容编码。
>   - Accept-Language，说明用户代理能够处理的自然语言集。
>   - Content-Type，说明实现主体的媒体类型。
>   - Content-Length，说明实现主体的大小。
>   - Connection，连接管理，可以是Keep-Alive或close。
>
> - **空行**，请求头部后面的空行是必须的即使第四部分的请求数据为空，也必须有空行。
>
> - **请求数据**也叫主体，可以添加任意的其他数据。



### **响应报文**

HTTP响应也由四个部分组成，分别是：状态行、消息报头、空行和响应正文。

```bash
HTTP/1.1 200 OK
Date: Fri, 22 May 2009 06:07:21 GMT
Content-Type: text/html; charset=UTF-8
空行
<html>
      <head></head>
      <body>
            <!--body goes here-->
      </body>
</html>
```

> - 状态行，由HTTP协议版本号， 状态码， 状态消息 三部分组成。
>   第一行为状态行，（HTTP/1.1）表明HTTP版本为1.1版本，状态码为200，状态消息为OK。
> - 消息报头，用来说明客户端要使用的一些附加信息。
>   第二行和第三行为消息报头，Date:生成响应的日期和时间；Content-Type:指定了MIME类型的HTML(text/html),编码类型是UTF-8。
> - 空行，消息报头后面的空行是必须的。
> - 响应正文，服务器返回给客户端的文本信息。空行后面的html部分为响应正文。



## recv 函数

`recv` 函数用于从一个已连接的套接字中接收数据，其函数原型如下：

```c++
#include <sys/socket.h>

ssize_t recv(int sockfd, void *buf, size_t len, int flags);
```

其中，参数含义如下：

- `sockfd`：接收端的套接字文件描述符；
- `buf`：存储接收数据的缓冲区；
- `len`：接收数据的缓冲区长度；
- `flags`：接收标志位。

函数返回值为接收到的数据长度，如果返回值为 0 表示连接已关闭，如果返回值为 -1 表示接收数据发生错误，具体的错误原因可以通过查看全局变量 `errno` 的值来确定。

其中 `flags` 参数可以设置为以下值之一：

- `MSG_CONFIRM`：数据包可能会被截断；
- `MSG_DONTWAIT`：在非阻塞模式下进行接收操作，当没有数据可读取时立即返回；
- `MSG_PEEK`：不清空缓冲区，只是从中读取数据；
- `MSG_WAITALL`：接收到指定长度的数据或者对端关闭连接才返回。

需要注意的是，`recv` 函数会将接收到的数据放入缓冲区 `buf` 中，因此在调用该函数前需要确保缓冲区 `buf` 的大小至少为接收数据的长度 `len`。此外，`recv` 函数的调用可能会阻塞当前进程，直到有数据可读取或者超时，因此需要在合适的地方使用非阻塞模式或者设置适当的超时时间。



TinyServer 的 `http::read_once()` 中，`bytes_read=recv(m_sockfd,m_read_buf+m_read_idx,READ_BUFFER_SIZE-m_read_idx,0);` 最后一个`参数传入 0 `表示：不希望使用任何特殊选项，仅仅希望执行一个普通的阻塞接收操作。由于recv函数是一个阻塞函数，因此会一直等待直到接收到至少一个字节的数据或者发生了错误才返回。因此，这个函数调用会一直阻塞，直到接收到数据或者发生了错误。

## errno

- EAGAIN：EAGAIN 是 errno.h 头文件中定义的一个宏，表示“资源暂时不可用”，通常在非阻塞 IO 中使用。

  在非阻塞 IO 中，如果没有数据可读或可写，会返回 EAGAIN 错误，提示调用者此时的资源还不可用，需要稍后再试。这种情况下，调用者可以通过重新尝试操作或等待一段时间后再次尝试来解决问题。

- EWOULDBLOCK：`EWOULDBLOCK` 和 `EAGAIN` 在大多数情况下是等价的，表示一个非阻塞的操作（比如读、写、接收、发送等）无法立即完成，因为没有可用的数据或缓冲区已满，但并不表示发生了错误。在这种情况下，可以重新尝试操作，直到它成功为止，或者等待一段时间后再尝试。



## accept 相关疑问

TinyWebServer 中 

`int connfd = accept(listenfd, (struct sockaddr *)&client_address, &client_addrlength);`

随后

`users[connfd].init(*connfd*, *client_address*, m_root, m_CONNTrigmode, m_close_log, m_user, m_passWord, m_databaseName);`



> 返回的 confd 会递增吗？

在多次调用 `accept()` 函数时，如果客户端连接过程中没有出现中断，则返回的文件描述符应该是递增的，因为每个新连接都会分配一个新的套接字描述符。但是，如果在连接过程中发生了某种错误或连接中断，则可能会重复返回相同的文件描述符。因此，为了避免这种情况，我们通常会在处理完每个新连接后关闭对应的套接字描述符，以避免使用相同的文件描述符。



## 参考

- [最新版Web服务器项目详解 - 04 http连接处理（上）](https://mp.weixin.qq.com/s/BfnNl-3jc_x5WPrWEJGdzQ)
- [最新版Web服务器项目详解 - 05 http连接处理（中）](https://mp.weixin.qq.com/s/wAQHU-QZiRt1VACMZZjNlw)
- [最新版Web服务器项目详解 - 06 http连接处理（下）](https://mp.weixin.qq.com/s/451xNaSFHxcxfKlPBV3OCg)



## 写在最后

感谢你在茫茫人海中找到我🕵🏼

<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>

<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.3.1/css/all.css" integrity="sha384-mzrmE5qonljUremFsqc01SB46JvROS7bZs3IO2EmfFsd15uHvIt+Y8vEf7N7fWAU" crossorigin="anonymous">

<span id="busuanzi_container_page_pv">🎉你是第 <span id="busuanzi_value_page_pv"><i class="fa fa-spinner fa-spin"></i>  </span> 个读者

㊗️ 你平安喜乐，顺遂无忧！

希望你读完有所收获～

🥂🥂🥂 
