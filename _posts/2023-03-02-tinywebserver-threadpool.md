---
layout: post
title: TinyWebServer 相关函数使用与样例 [半同步半反应堆线程池]
date: 2023-03-02 16:07 +0800
categories:
- 项目
- TinyWebServer
tags:
- C++
- 线程
---



## pthread_create 函数

pthread_create 是 POSIX 线程库中用于创建一个新线程的函数。

函数定义：

```c++
#include <pthread.h>

int pthread_create(pthread_t *thread, const pthread_attr_t *attr,
                   void *(*start_routine) (void *), void *arg);
```

参数名称和解释：

- `thread`: 一个指向 pthread_t 类型变量的指针，用于存储新线程的 ID。
- `attr`: 一个指向 pthread_attr_t 类型的指针，用于指定新线程的属性，如线程栈大小、线程调度策略等。
- `start_routine`: 一个函数指针，指向新线程要执行的函数。
- `arg`: 一个指针，传递给新线程要执行的函数作为参数。

所需的头文件：

```c++
#include <pthread.h>
```

函数作用：pthread_create 函数用于创建一个新的线程，并将其加入到调用进程中。新线程从 start_routine 函数的地址开始执行，传递给 start_routine 的参数为 arg。线程创建成功后，线程 ID 会存储在由 thread 指向的内存位置中。

函数返回值：如果函数执行成功，返回 0。如果返回其他值，则表示出现了错误。可以使用 strerror 函数将错误码转换为错误消息。

示例代码：

```c++
#include <pthread.h>
#include <iostream>

void* thread_func(void* arg) {
  std::cout << "Hello from thread" << std::endl;
  return NULL;
}

int main() {
  pthread_t tid;
  pthread_create(&tid, NULL, thread_func, NULL);

  pthread_join(tid, NULL);
  std::cout << "Thread finished" << std::endl;

  return 0;
}

```

输出结果：

```vb
Hello from thread
Thread finished
```



## pthread_detach 函数

函数定义：

```c++
int pthread_detach(pthread_t thread);
```

参数名称和解释：

- `thread`: 待分离的线程标识符

所需头文件：

```c++
#include <pthread.h>
```

函数作用：

`pthread_detach()`函数将指定的线程标识符对应的线程分离，使得该线程可以自动回收资源，避免留下僵尸线程。一旦线程被分离，就不能再次被连接（pthread_join）。

函数返回值：

- 如果函数调用成功，返回0；
- 如果出现错误，返回非0的错误码。

示例代码：

```c++
#include <iostream>
#include <pthread.h>

using namespace std;

void* thread_func(void* arg) {
    cout << "Thread is running!" << endl;
    pthread_exit(nullptr);
}

int main() {
    pthread_t tid;
    int ret = pthread_create(&tid, nullptr, thread_func, nullptr);
    if (ret != 0) {
        cerr << "Failed to create thread!" << endl;
        return 1;
    }

    ret = pthread_detach(tid);
    if (ret != 0) {
        cerr << "Failed to detach thread!" << endl;
        return 1;
    }

    cout << "Thread is detached!" << endl;

    pthread_exit(nullptr);
}

```

在上述代码中，我们创建一个新线程，并在主线程中将该线程分离。最终，主线程退出，由于新线程已经被分离，不会影响整个程序的运行。



## 参考

- [最新版Web服务器项目详解 - 02 半同步半反应堆线程池（上）](https://mp.weixin.qq.com/s?__biz=MzAxNzU2MzcwMw==&mid=2649274278&idx=4&sn=caa323faf0c51d882453c0e0c6a62282&chksm=83ffbefeb48837e841a6dbff292217475d9075e91cbe14042ad6e55b87437dcd01e6d9219e7d&scene=0&xtrack=1#rd)
- [最新版Web服务器项目详解 - 03 半同步半反应堆线程池（下）](https://mp.weixin.qq.com/s/PB8vMwi8sB4Jw3WzAKpWOQ)



## 博主寄语

感谢你在茫茫人海中找到我🕵🏼

<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>

<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.3.1/css/all.css" integrity="sha384-mzrmE5qonljUremFsqc01SB46JvROS7bZs3IO2EmfFsd15uHvIt+Y8vEf7N7fWAU" crossorigin="anonymous">

<span id="busuanzi_container_page_pv">🎉你是第 <span id="busuanzi_value_page_pv"><i class="fa fa-spinner fa-spin"></i>  </span> 个读者

㊗️你读完有所收获～

🥂🥂🥂 
