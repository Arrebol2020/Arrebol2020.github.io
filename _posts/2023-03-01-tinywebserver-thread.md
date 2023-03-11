---
layout: post
title: TinyWebServer 相关函数使用与样例 [线程同步机制]
date: 2023-03-01 20:58 +0800
categories:
- 项目
- TinyWebServer
tags:
- C++
- 信号量
---

## 竟态

竟态（Race Condition）是指多个进程或线程在对共享资源进行读写时，由于对共享资源的访问顺序不确定或事件差等因素导致的错误行为。具体来说，当两个或多个进程或线程试图同时访问同一共享资源时，它们之间可能会产生竟态，从而导致程序出现不可预测的结果

竟态是一种非常难以调试和解决的问题，因为竟态通常只在特定的时间和特定的运行环境下才会出现，因此可能需要多次运行程序才能重现问题。`为避免竟态，需要使用各种同步机制，例如互斥锁、条件变量、信号量等，来保护共享资源的访问，确保只有一个进程或线程能够访问该资源`



## 信号量

信号量是一种用于进程或线程间同步和互斥的机制，通常包括两种操作：P（wait）和 V（signal）。

P 操作会使得信号量的值减 1，如果此时信号量的值小于 0，则调用进程或线程会被阻塞，等待其他进程或线程对信号量进行 V 操作，使得信号量的值大于 0，此时阻塞的进程或线程才能继续执行。

V 操作会使得信号量的值加 1，如果此时信号量的值小于等于 0，则会唤醒阻塞在该信号量上的某个进程或线程。

在 C++ 中，常用的信号量函数包括 `sem_init()`、`sem_destroy()`、`sem_wait()` 和 `sem_post()`，它们的具体定义、参数名称和解释、所要的头文件、函数作用和函数返回值如下：

### sem_init 函数

函数定义：

```c++
int sem_init(sem_t *sem, int pshared, unsigned int value);
```

- 参数名称：
  - `sem`：指向要初始化的信号量的指针。
  - `pshared`：指定信号量的共享方式。如果值为 0，则信号量将在进程内部共享。如果值为非 0，则信号量可以在不同进程之间共享，需要使用共享内存。
  - `value`：指定信号量的初值。
- 所要的头文件：`<semaphore.h>`
- 函数作用：初始化一个信号量。
- 函数返回值：若成功则返回 0，否则返回 -1。

### sem_destroy 函数

函数定义：

```c++
int sem_destroy(sem_t *sem);
```

- 参数名称：
  - `sem`：指向要销毁的信号量的指针。
- 所要的头文件：`<semaphore.h>`
- 函数作用：销毁一个信号量。
- 函数返回值：若成功则返回 0，否则返回 -1。

### sem_wait 函数

 函数定义：

```c++
int sem_wait(sem_t *sem);
```

- 参数名称：
  - `sem`：指向要操作的信号量的指针。
- 所要的头文件：`<semaphore.h>`
- 函数作用：对信号量进行 P 操作，如果信号量的值小于等于 0，则会阻塞当前线程。
- 函数返回值：若成功则返回 0，否则返回 -1。

### sem_post 函数

函数定义：

```c++
int sem_post(sem_t *sem);
```

- 参数名称：
  - `sem`：指向要操作的信号量的指针。
- 所要的头文件：`<semaphore.h>`
- 函数作用：对信号量进行 V 操作，如果信号量的值小于等于 0，则会唤醒阻塞在该信号量上的某个线程。
- 函数返回值：若成功则返回 0，否则返回 -1。



下面是一个简单的 C++ 样例，使用信号量来控制线程的并发执行：

```c++
#include <iostream>
#include <thread>
#include <semaphore.h>

sem_t sem;

void thread_func(int id) {
    sem_wait(&sem);  // 对信号量进行 P 操作
    std::cout << "Thread " << id << " starts." << std::endl;
    // 执行一些任务
    std::cout << "Thread " << id << " ends." << std::endl;
    sem_post(&sem);  // 对信号量进行 V 操作
}

int main() {
    int num_threads = 5;
    sem_init(&sem, 0, num_threads);  // 初始化信号量的值为 num_threads
    std::thread threads[num_threads];
    for (int i = 0; i < num_threads; i++) {
        threads[i] = std::thread(thread_func, i);
    }
    for (int i = 0; i < num_threads; i++) {
        threads[i].join();
    }
    sem_destroy(&sem);  // 销毁信号量
    return 0;
}

```

在该样例中，主线程创建了 5 个子线程，并通过 `sem_init()` 函数初始化了一个初始值为 5 的信号量。每个子线程先执行 `sem_wait()` 函数，对信号量进行 P 操作，如果信号量的值为 0，则会被阻塞。当有一个子线程执行完任务后，执行 `sem_post()` 函数，对信号量进行 V 操作，唤醒一个阻塞的子线程。当所有子线程都执行完任务后，主线程执行 `sem_destroy()` 函数，销毁信号量。



## 互斥量

互斥锁,也成互斥量,可以保护关键代码段,以确保独占式访问.当进入关键代码段,获得互斥锁将其加锁;离开关键代码段,唤醒等待该互斥锁的线程

互斥量是一种用于保护临界区的同步机制，可以确保同一时刻只有一个线程访问共享资源。当一个线程访问共享资源时，需要先获取互斥量的锁，其他线程需要等待该锁释放才能继续执行。

### pthread_mutex_init 函数

函数定义：

```c++
int pthread_mutex_init(pthread_mutex_t *mutex, const pthread_mutexattr_t *attr);
```

- 参数名称：
  - `mutex`：指向要初始化的互斥量的指针。
  - `attr`：指向互斥量属性对象的指针，通常设置为 NULL。
- 所要的头文件：`<pthread.h>`
- 函数作用：初始化互斥量。
- 函数返回值：若成功则返回 0，否则返回一个正整数的错误码。

### pthread_mutex_destroy 函数

函数定义：

```c++
int pthread_mutex_destroy(pthread_mutex_t *mutex);
```

- 参数名称：
  - `mutex`：指向要销毁的互斥量的指针。
- 所要的头文件：`<pthread.h>`
- 函数作用：销毁互斥量。
- 函数返回值：若成功则返回 0，否则返回一个正整数的错误码。

### pthread_mutex_lock 函数

函数定义：

```c++
int pthread_mutex_lock(pthread_mutex_t *mutex);
```

- 参数名称：
  - `mutex`：指向要加锁的互斥量的指针。
- 所要的头文件：`<pthread.h>`
- 函数作用：给互斥量加锁，如果互斥量已经被锁住，则阻塞当前线程，直到互斥量被解锁。
- 函数返回值：若成功则返回 0，否则返回一个正整数的错误码。

### pthread_mutex_unlock 函数

函数定义：

```c++
int pthread_mutex_unlock(pthread_mutex_t *mutex);
```

- 参数名称：
  - `mutex`：指向要解锁的互斥量的指针。
- 所要的头文件：`<pthread.h>`
- 函数作用：解锁互斥量，如果有等待该互斥量的线程，则唤醒其中的一个线程。
- 函数返回值：若成功则返回 0，否则返回一个正整数的错误码。

下面是一个完整的代码样例，演示了如何使用互斥量来保护共享资源的访问：

```c++
#include <iostream>
#include <thread>
#include <mutex>

std::mutex mtx;
int shared_data = 0;

void thread_func(int id) {
    mtx.lock();  // 加锁
    std::cout << "Thread " << id << " starts." << std::endl;
    shared_data++;
    std::cout << "Thread " << id << " increases shared_data to " << shared_data << std::endl;
    mtx.unlock();  // 解锁
}

int main() {
    int num_threads = 5;
    std::thread threads[num_threads];
    for (int i = 0; i < num_threads; i++) {
        threads[i] = std::thread(thread_func, i);
    }
    for (int i = 0; i < num_threads; i++) {
        threads[i].join();
    }
    std::cout << "Final value of shared_data is " << shared_data << std::endl;
    return 0;
}

```

在这个例子中，共享资源是一个整型变量 `shared_data`，每个线程都会将它增加一，但由于互斥量的保护，不会有两个线程同时访问和修改它。输出结果如下：

```vb
Thread 0 starts.
Thread 1 starts.
Thread 2 starts.
Thread 3 starts.
Thread 4 starts.
Thread 2 increases shared_data to 1
Thread 4 increases shared_data to 2
Thread 0 increases shared_data to 3
Thread 1 increases shared_data to 4
Thread 3 increases shared_data to 5
Final value of shared_data is 5
```

可以看到，每个线程都成功地增加了共享资源的值，最终的结果也是正确的。



## 条件变量

### pthread_cond_init 函数

函数定义：

```c++
int pthread_cond_init(pthread_cond_t *cond, const pthread_condattr_t *attr);
```

- 参数名称：
  - cond：指向条件变量对象的指针。
  - attr：指向线程条件属性对象的指针。一般为 NULL。

- 所需头文件：`#include <pthread.h>`

- 函数作用： 初始化条件变量对象，设置相关属性。

- 函数返回值： 成功返回0，失败返回错误号。

### pthread_cond_destory 函数

函数定义：

```c++
int pthread_cond_destroy(pthread_cond_t *cond);
```

- 参数名称：
  - cond：指向条件变量对象的指针。

- 所需头文件：`#include <pthread.h>`

- 函数作用： 销毁条件变量对象，释放资源。

- 函数返回值： 成功返回0，失败返回错误号。

### pthread_cond_broadcast 函数

函数定义：

```c++
int pthread_cond_broadcast(pthread_cond_t *cond);
```

- 参数名称：
  - cond：指向条件变量对象的指针。

- 所需头文件：`#include <pthread.h>`

- 函数作用： 唤醒所有在条件变量上等待的线程。

- 函数返回值： 成功返回0，失败返回错误号。

### pthread_cond_wait 函数

函数定义：

```c++
int pthread_cond_wait(pthread_cond_t *cond, pthread_mutex_t *mutex);
```

- 参数名称：
  - cond：指向条件变量对象的指针。
  - mutex：指向互斥锁对象的指针，用于保护条件变量。

- 所需头文件：`#include <pthread.h>`

- 函数作用： 让当前线程阻塞在条件变量上等待唤醒。

- 函数返回值： 成功返回0，失败返回错误号。

### pthread_cond_signal 函数

函数定义：

```c++
int pthread_cond_signal(pthread_cond_t *cond);
```

参数说明：

- `cond`：指向条件变量的指针。

函数作用：

- 发送一个信号通知正在等待条件变量的线程。

  > 需要注意的是，pthread_cond_signal 只会通知一个等待该条件变量的线程，如果有多个线程在等待，则只有一个线程会收到通知，其余线程还会继续等待，直到下一次收到信号
  >
  > 此外，pthread_cond_signal 的使用需要注意以下几点：
  >
  > - 必须在已经获得与条件变量相关的互斥锁之后才能调用该函数。
  > - 如果没有等待该条件变量的线程，调用该函数也不会产生任何作用。

函数返回值：

- 成功返回 0，失败返回错误码。

简单样例：

```c++
#include <iostream>
#include <pthread.h>

using namespace std;

pthread_cond_t cond = PTHREAD_COND_INITIALIZER;
pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;

void* thread_func1(void* arg) {
    pthread_mutex_lock(&mutex);
    cout << "Thread 1 is waiting." << endl;
    pthread_cond_wait(&cond, &mutex);
    cout << "Thread 1 is awake." << endl;
    pthread_mutex_unlock(&mutex);
    pthread_exit(NULL);
}

void* thread_func2(void* arg) {
    pthread_mutex_lock(&mutex);
    cout << "Thread 2 is waking up all threads." << endl;
    pthread_cond_broadcast(&cond);
    pthread_mutex_unlock(&mutex);
    pthread_exit(NULL);
}

int main() {
    pthread_t thread1, thread2;
    pthread_create(&thread1, NULL, thread_func1, NULL);
    pthread_create(&thread2, NULL, thread_func2, NULL);

    pthread_join(thread1, NULL);
    pthread_join(thread2, NULL);

    return 0;
}

```

在此样例中，创建了两个线程，线程 1 等待条件变量 cond 被唤醒，线程 2 在等待一段时间后调用pthread_cond_broadcast 函数唤醒所有等待 cond 的线程，线程 1 被唤醒后输出信息，整个程序结束。输出结果如下：

```vb
Thread 1 is waiting.
Thread 2 is waking up all threads.
Thread 1 is awake.
```



> 为什么两个线程都有 pthread_mutex_lock 而不会阻塞

在 `thread_func1` 中，当线程调用 `pthread_cond_wait` 函数时，线程会先解锁相关的互斥量，然后阻塞在条件变量上等待信号通知，当收到信号通知后，线程会重新获得互斥量并继续执行。

需要注意的是，在调用 pthread_cond_wait 函数之前必须先加锁，这是因为在等待信号时，可能会有其他线程对共享资源进行修改，此时如果没有加锁，会导致数据不一致或者数据竞争的问题。

总结来说，pthread_cond_wait 函数不是自动释放锁，而是在等待期间主动释放锁，并在收到信号后重新获得锁。



## 功能

锁机制的功能：实现多线程同步，通过锁机制，确保任一时刻只能有一个线程能进入关键代码段



封装的功能：

- 类中主要是 Linux下三种锁进行封装，将锁的创建于销毁函数封装在类的构造与析构函数中，实现 RAII 机制

  ```c++
  class sem{
      public:
          //构造函数
          sem()
          {
              //信号量初始化
              if(sem_init(&m_sem,0,0)!=0){
                  throw std::exception();
              }
          }
          //析构函数
          ~sem()
          {
              //信号量销毁
              sem_destroy(&m_sem);
          }
      private:
          sem_t m_sem;
  };
  ```

- 将重复使用的代码封装为函数，减少代码的重复，使其更简洁

  ```c++
     //条件变量的使用机制需要配合锁来使用
     //内部会有一次加锁和解锁
     //封装起来会使得更加简洁
     bool wait()
     {
         int ret=0;
         pthread_mutex_lock(&m_mutex);
         ret=pthread_cond_wait(&m_cond,&m_mutex);
         pthread_mutex_unlock(&m_mutex);
         return ret==0;
     }
     bool signal()
     {
         return pthread_cond_signal(&m_cond)==0;
     }
  ```

  

## 参考

- [最新版Web服务器项目详解 - 01 线程同步机制封装类](https://mp.weixin.qq.com/s?__biz=MzAxNzU2MzcwMw==&mid=2649274278&idx=3&sn=5840ff698e3f963c7855d702e842ec47&chksm=83ffbefeb48837e86fed9754986bca6db364a6fe2e2923549a378e8e5dec6e3cf732cdb198e2&scene=0&xtrack=1#rd)

