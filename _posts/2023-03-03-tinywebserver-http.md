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



## 参考

- [最新版Web服务器项目详解 - 04 http连接处理（上）](https://mp.weixin.qq.com/s/BfnNl-3jc_x5WPrWEJGdzQ)
- [最新版Web服务器项目详解 - 05 http连接处理（中）](https://mp.weixin.qq.com/s/wAQHU-QZiRt1VACMZZjNlw)
- [最新版Web服务器项目详解 - 06 http连接处理（下）](https://mp.weixin.qq.com/s/451xNaSFHxcxfKlPBV3OCg)



## 写在最后

感谢你在茫茫人海中找到我🕵🏼

<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>

<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.3.1/css/all.css" integrity="sha384-mzrmE5qonljUremFsqc01SB46JvROS7bZs3IO2EmfFsd15uHvIt+Y8vEf7N7fWAU" crossorigin="anonymous">

<span id="busuanzi_container_page_pv">🎉你是第 <span id="busuanzi_value_page_pv"><i class="fa fa-spinner fa-spin"></i>  </span> 个读者 {: .prompt-tip }

㊗️ 你平安喜乐，顺遂无忧！

希望你读完有所收获～

🥂🥂🥂 
