---
layout: post
title: TinyWebServer 相关函数使用与样例 [日志]
date: 2023-03-10 15:02 +0800
categories:
- 项目
- TinyWebServer
tags:
- C++
---

本项目中，使用单例模式创建日志系统，对服务器运行状态、错误信息和访问数据进行记录，该系统可以实现按天分类，超行分类功能，可以根据实际情况分别使用同步和异步写入两种方式

其中异步写入方式，将生产者-消费者模型封装为阻塞队列，创建一个写线程，工作线程将要写的内容push进队列，写线程从队列中取出内容，写入日志文件

日志系统大致可以分成两部分，其一是单例模式与阻塞队列的定义，其二是日志类的定义与使用



## 单例模式

单例模式作为最常用的设计模式之一，保证一个类仅有一个实例，并提供一个访问它的全局访问点，该实例被所有程序模块共享

实现思路：私有化它的构造函数，以防止外界创建单例类的对象；使用类的私有静态指针变量指向类的唯一实例，并用一个公有的静态方法获取该实例

单例模式有两种实现方法，分别是懒汉和饿汉模式。顾名思义，懒汉模式，即非常懒，不用的时候不去初始化，所以在第一次被使用时才进行初始化；饿汉模式，即迫不及待，在程序运行时立即初始化



### 经典的线程安全懒汉模式

```c++
#include <pthread.h>

class single{
private:
    // 私有化静态指针变量指向唯一实例
    static single *p;

    // 静态锁，是由于静态函数只能访问静态成员
    static pthread_mutex_t lock;

    // 私有化构造函数
    single(){
        pthread_mutex_init(&lock, nullptr);
    }

    ~single(){}

public:
    // 共有静态方法获取实例
    static single* getinstance();
};

pthread_mutex_t single::lock;
single* single::p = nullptr;
single* single::getinstance(){
    if(nullptr == p)
    {
        pthread_mutex_lock(&lock);
        if(nullptr == p) {
            p = new single;
        }
        pthread_mutex_unlock(&lock);
    }

    return p;
}


```



> **`为什么要用双检测，只检测一次不行吗？`**

如果只检测一次，在每次调用获取实例的方法时，都需要加锁，这将严重影响程序性能。双层检测可以有效避免这种情况，仅在第一次创建单例的时候加锁，其他时候都不再符合NULL == p的情况，直接返回已创建好的实例



### 局部静态变量之线程安全懒汉模式

前面的双检测锁模式，写起来不太优雅，《Effective C++》（Item 04）中的提出另一种更优雅的单例模式实现，使用函数内的局部静态对象，这种方法不用加锁和解锁操作

```c++
class single{
private:
    single(){}
    ~single(){}

public:
    static single* getinstance();

};

single* single::getinstance(){
    static single obj;
    return &obj;
}
```



> **`这种方法不加锁会不会造成线程安全问题`**

C++0X以后，要求编译器保证内部静态变量的线程安全性，故C++0x之后该实现是线程安全的，C++0x之前仍需加锁，其中C++0x是C++11标准成为正式标准之前的草案临时名字

所以，如果使用C++11之前的标准，还是需要加锁，这里同样给出加锁的版本

```c++
class single{
private:
    static pthread_mutex_t lock;
    single(){
      pthread_mutex_init(&lock, NULL);
    }
    ~single(){}

public:
    static single* getinstance();

};

pthread_mutex_t single::lock;
single* single::getinstance(){
    pthread_mutex_lock(&lock);
    static single obj;
  	pthread_mutex_unlock(&lock);
    return &obj;
}
```



### 饿汉模式

饿汉模式不需要用锁，就可以实现线程安全。原因在于，在程序运行时就定义了对象，并对其初始化。之后，不管哪个线程调用成员函数getinstance()，都只不过是返回一个对象的指针而已。所以是线程安全的，不需要在获取实例的成员函数中加锁

```c++
class single{
private:
    static single* p;
    single(){}
    ~single(){}

public:
    static single* getinstance();

};
single* single::p = new single();
single* single::getinstance(){
    return p;
}

//测试方法
int main(){

    single *p1 = single::getinstance();
    single *p2 = single::getinstance();

    if (p1 == p2)
        cout << "same" << endl;

    system("pause");
    return 0;
}
```



饿汉模式虽好，但其存在隐藏的问题，在于`非静态对象（函数外的static对象）在不同编译单元中的初始化顺序是未定义的`。如果在初始化完成之前调用 getInstance() 方法会返回一个未定义的实例



## 条件变量与生产者-消费者模型

条件变量提供了一种线程间的通知机制，当某个共享数据达到某个值时,唤醒等待这个共享数据的线程。

### 基础API

- pthread_cond_init 函数，用于初始化条件变量
- pthread_cond_destory 函数，销毁条件变量
- pthread_cond_broadcast 函数，以广播的方式唤醒**所有**等待目标条件变量的线程
- pthread_cond_wait 函数，用于等待目标条件变量。该函数调用时需要传入 **mutex 参数(加锁的互斥锁)** ，函数执行时，先把调用线程放入条件变量的请求队列，然后将互斥锁 mutex 解锁，当函数成功返回为 0 时，表示重新抢到了互斥锁，互斥锁会再次被锁上， **也就是说函数内部会有一次解锁和加锁操作**.

使用pthread_cond_wait方式如下：

```c++
pthread _mutex_lock(&mutex)

while(线程执行的条件是否成立){
    pthread_cond_wait(&cond, &mutex);
}

pthread_mutex_unlock(&mutex);
```

pthread_cond_wait 执行后的内部操作分为以下几步：

- 将线程放在条件变量的请求队列后，内部解锁
- 线程等待被 pthread_cond_broadcast 信号唤醒或者 pthread_cond_signal 信号唤醒，唤醒后去竞争锁
- 若竞争到互斥锁，内部再次加锁



### 为什么使用前要加锁

多线程访问，为了避免资源竞争，所以要加锁，使得每个线程互斥的访问共有资源



### pthread_cond_wait 内部为什么要解锁

如果 while 或者 if 判断的时候，满足执行条件，线程便会调用 pthread_cond_wait 阻塞自己，此时它还在持有锁，如果他不解锁，那么其他线程将会无法访问公有资源。 

具体到 pthread_cond_wait 的内部实现，当 pthread_cond_wait 被调用线程阻塞的时候，pthread_cond_wait 会自动释放互斥锁。



### 为什么要调用线程放入条件变量的请求队列后再解锁

线程是并发执行的，如果在把调用线程 A 放在等待队列之前，就释放了互斥锁，这就意味着其他线程比如线程 B 可以获得互斥锁去访问公有资源，这时候线程 A 所等待的条件改变了，但是它没有被放在等待队列上，导致 A 忽略了等待条件被满足的信号。

倘若在线程A调用 pthread_cond_wait 开始，到把 A 放在等待队列的过程中，都持有互斥锁，其他线程无法得到互斥锁，就不能改变公有资源。



### 为什么最后还要加锁

将线程放在条件变量的请求队列后，将其解锁，此时等待被唤醒，若成功竞争到互斥锁，再次加锁。



### 为什么判断线程执行的条件用 while 而不是 if

一般来说，在多线程资源竞争的时候，在一个使用资源的线程里面（消费者）判断资源是否可用，不可用，便调用 pthread_cond_wait，在另一个线程里面（生产者）如果判断资源可用的话，则调用 pthread_cond_signal 发送一个资源可用信号。

在 wait 成功之后，资源就一定可以被使用么？答案是否定的，如果同时有两个或者两个以上的线程正在等待此资源，wait 返回后，资源可能已经被使用了。

再具体点，有可能多个线程都在等待这个资源可用的信号，信号发出后只有一个资源可用，但是有 A，B 两个线程都在等待，B 比较速度快，获得互斥锁，然后加锁，消耗资源，然后解锁，之后 A 获得互斥锁，但 A 回去发现资源已经被使用了，它便有两个选择，一个是去访问不存在的资源，另一个就是继续等待，那么继续等待下去的条件就是使用 while，要不然使用 if 的话 pthread_cond_wait 返回后，就会顺序执行下去。

所以，在这种情况下，应该使用 while 而不是 if:

```c++
while(resource == FALSE)
    pthread_cond_wait(&cond, &mutex);
```

如果只有一个消费者，那么使用if是可以的。



fputs

```c++
#include <stdio.h>
int fputs(const char *str, FILE *stream);
```

- str，一个数组，包含了要写入的以空字符终止的字符序列
- stream，指向FILE对象的指针，该FILE对象标识了要被写入字符串的流



可变宏参数 _\_VA_ARGS\_\_

_\_VA_ARGS\_\_ 是一个可变参数的宏，定义时宏定义中参数列表的最后一个参数为省略号，在实际使用时会发现有时会加##，有时又不加

```c++
//最简单的定义
#define my_print1(...)  printf(__VA_ARGS__)

//搭配va_list的format使用
#define my_print2(format, ...) printf(format, __VA_ARGS__)  
#define my_print3(format, ...) printf(format, ##__VA_ARGS__)
```

_\_VA_ARGS\_\_  宏前面加上##的作用在于，当可变参数的个数为0时，这里printf参数列表中的的##会把前面多余的","去掉，否则会编译出错，建议使用后面这种，使得程序更加健壮



fflush

```c++
#include <stdio.h>
int fflush(FILE *stream);
```

fflush() 会强迫将缓冲区内的数据写回参数 stream 指定的文件中，如果参数 stream 为NULL，`fflush() 会将所有打开的文件数据更新`

在使用多个输出函数连续进行多次输出到控制台时，有可能下一个数据在上一个数据还没输出完毕，还在输出缓冲区中时，下一个 printf 就把另一个数据加入输出缓冲区，结果冲掉了原来的数据，出现输出错误。

在 prinf() 后加上 fflush(stdout);  强制马上输出到控制台，可以避免出现上述错误



## 参考

- 最新版Web服务器项目详解 - 09 日志系统（上）：https://mp.weixin.qq.com/s/IWAlPzVDkR2ZRI5iirEfCg
- 最新版Web服务器项目详解 - 10 日志系统（下）：https://mp.weixin.qq.com/s/f-ujwFyCe1LZa3EB561ehA



## 写在最后

感谢你在茫茫人海中找到我🕵🏼

<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>

<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.3.1/css/all.css" integrity="sha384-mzrmE5qonljUremFsqc01SB46JvROS7bZs3IO2EmfFsd15uHvIt+Y8vEf7N7fWAU" crossorigin="anonymous">

<span id="busuanzi_container_page_pv">🎉你是第 <span id="busuanzi_value_page_pv"><i class="fa fa-spinner fa-spin"></i>  </span> 个读者

㊗️ 你平安喜乐，顺遂无忧！

希望你读完有所收获～

🥂🥂🥂 
