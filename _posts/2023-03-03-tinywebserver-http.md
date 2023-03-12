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
