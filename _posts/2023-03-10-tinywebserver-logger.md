---
layout: post
title: TinyWebServer 相关函数使用与样例 [日志]
date: 2023-03-10 15:02 +0800
categories:
- 项目
tags:
- TinyWebServer
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
class single{
private:
    //私有静态指针变量指向唯一实例
    static single *p;

    //静态锁，是由于静态函数只能访问静态成员
    static pthread_mutex_t lock;

    //私有化构造函数
    single(){
        pthread_mutex_init(&lock, NULL);
    }
    ~single(){}

public:
    //公有静态方法获取实例
    static single* getinstance();

};

pthread_mutex_t single::lock;

single* single::p = NULL;
single* single::getinstance(){
    if (NULL == p){
        pthread_mutex_lock(&lock);
        if (NULL == p){
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
 1class single{
 2private:
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



饿汉模式虽好，但其存在隐藏的问题，在于非静态对象（函数外的static对象）在不同编译单元中的初始化顺序是未定义的。如果在初始化完成之前调用 getInstance() 方法会返回一个未定义的实例



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
- 
