---
layout: post
title: C++ 内存管理
date: 2023-03-20 09:43 +0800
categories:
- 学习随笔
- C++
tags:
- C++
- 内存管理
---



C++ 中的内存管理是指在运行时分配和释放内存的过程。C++ 提供了两个操作符来实现动态内存管理: **new** 和 **delete**。new 操作符用于在堆上为变量或数组分配内存，并返回分配的内存地址。delete 操作符用于释放由 new 分配的内存，并避免内存泄漏。

>除了 new 和 delete，C++ 还提供了一些类模板来封装不同的内存分配策略。这些类模板称为 **分配器**，它们允许通用容器将内存管理和数据本身解耦。例如，标准库中的 vector、list、map 等容器都可以使用不同的分配器来控制它们的元素如何在堆上分配和释放。
{: .prompt-info}

C++ 中的内存管理是一项强大而灵活的功能，但也需要程序员注意避免一些常见的错误，如`空指针解引用、野指针、重复释放、未初始化等`。

   

## new delete 与 malloc free

new 和 delete 是 C++ 提供的操作符，用于在运行时分配和释放内存。malloc 和 free 是 C 提供的库函数，也可以在 C++ 中使用，用于同样的目的。

它们之间有一些区别，如:

- new 和 delete 可以初始化内存，而 malloc 和 free 不会。

- new 和 delete 可以`调用类的构造函数和析构函数`，而 malloc 和 free 不会。

- new 和 delete 的语法更简单，`不需要手动计算内存大小或强制类型转换`。

- new 和 delete `可以被重载`，而 malloc 和 free 不可以。

- 
  malloc 失败时返回 NULL，new 失败时抛出 bad_alloc 异常。
  
- malloc 从堆中分配内存，new 从自由存储区分配内存。

- malloc/free 是函数，new/delete 是操作符。

   

分配与释放内存：

分配和释放动态内存的方法取决于你使用的是 C 语言还是 C++ 语言。在 C 语言中，你可以使用 malloc 和 free 函数来分配和释放动态内存。例如，如果你要分配一个 int 类型的变量，你可以这样写：

```c
int *p = (int *)malloc(sizeof(int)); // 分配一个 int 大小的内存空间，并让指针 p 指向它
*p = 10; // 在分配的内存空间中存储一个值
free(p); // 释放分配的内存空间
```

在 C++ 语言中，你可以使用 new 和 delete 运算符来分配和释放动态内存。例如，如果你要分配一个 int 类型的变量，你可以这样写：

```c++
int *p = new int; // 分配一个 int 大小的内存空间，并让指针 p 指向它
*p = 10; // 在分配的内存空间中存储一个值
delete p; // 释放分配的内存空间
```

> 如果你要分配或释放一个数组，你需要使用 new[] 和 delete[] 运算符。例如，如果你要分配一个长度为 10 的 int 数组，你可以这样写：
>
> ```c++
> int *p = new int[10]; // 分配一个长度为 10 的 int 数组，并让指针 p 指向它
> for (int i = 0; i < 10; i++) {
>     p[i] = i; // 在数组中存储一些值
> }
> delete[] p; // 释放数组占用的内存空间
> ```
{: .prompt-danger}

​    

## 重载 new 和 delete

重载 new 和 delete 运算符的目的是`为了自定义内存分配和释放的方式，比如添加异常处理、提高数据安全性、优化内存管理等`。重载 new 和 delete 运算符可以是全局的，也可以是类的成员函数。如果是全局的，那么对所有使用这些运算符的对象都有效；如果是类的成员函数，那么只对该类及其派生类有效。

重载 new 和 delete 运算符的一般形式如下：

```c++
// 重载全局 new
void* operator new(size_t size) {
    // 在这里分配内存
}

// 重载全局 delete
void operator delete(void* p) {
    // 在这里释放内存
}

// 重载类的成员 new
class A {
public:
    void* operator new(size_t size) {
        // 在这里分配内存
    }
};

// 重载类的成员 delete
class A {
public:
    void operator delete(void* p) {
        // 在这里释放内存
    }
};
```

注意，如果要重载数组版本的 new[] 和 delete[] 运算符，需要在参数列表中加上一个 size_t 类型的参数。例如：

```c++
// 重载全局 new[]
void* operator new[](size_t size) {
    // 在这里分配内存
}

// 重载全局 delete[]
void operator delete[](void* p, size_t size) {
    // 在这里释放内存
}
```

   

样例：

假设我们有一个类 B，它有一个静态成员变量 pool，用来存储一块预先分配好的内存空间。我们可以重载 B 的 new 和 delete 运算符，`让它们从 pool 中分配和释放内存，而不是从堆中`。例如：

```c++
class B {
public:
    B() {
        cout << "B constructor" << endl;
    }
    ~B() {
        cout << "B destructor" << endl;
    }
    // 重载 new 运算符
    void* operator new(size_t size) {
        cout << "B operator new" << endl;
        return pool; // 从 pool 中返回内存地址
    }
    // 重载 delete 运算符
    void operator delete(void* p) {
        cout << "B operator delete" << endl;
        // 不需要释放内存，因为 pool 是静态的
    }
private:
    static char pool[]; // 静态成员变量，用来存储一块内存空间
};

char B::pool[100]; // 定义并初始化静态成员变量
```

这样，当我们用 new 来创建 B 的对象时，就会调用我们重载的 new 运算符，并从 pool 中返回内存地址。当我们用 delete 来销毁 B 的对象时，就会调用我们重载的 delete 运算符，并不需要释放内存。例如：

```c++
B *b = new B(); // 调用重载的 new 运算符，并从 pool 中返回内存地址
delete b; // 调用析构函数和重载的 delete 运算符，并不需要释放内存
```

这段代码的输出是：

```c++
B operator new
B constructor
B destructor
B operator delete
```

这说明我们成功地在已经分配好的内存空间中创建和销毁了 B 的对象。

​      

在 C++ 中，还有一种特殊的 new 运算符，叫做 placement new。它可以在已经分配好的内存空间中创建对象，而不需要再次分配内存。它有一个固定的形式：

```c++
void* operator new(size_t size, void* p) {
    return p;
}
```

使用 placement new 的时候，需要传入一个已经分配好的指针作为第二个参数。例如：

```c++
char *p = (char *)malloc(sizeof(A)); // 分配一个 A 大小的空间，并让指针 p 指向它
A *a = new(p) A(); // 在 p 指向的空间中创建一个 A 对象，并让指针 a 指向它
a->~A(); // 调用析构函数销毁对象 a 
free(p); // 释放指针 p 指向的空间
```

注意，在使用 placement new 的时候，不需要调用 delete 来释放对象，而是要手动调用析构函数来销毁对象，并且要使用原来分配空间时用到的方法来释放空间（比如 malloc 对应 free）。

> placement new 的优点是可以提高内存利用率，减少内存碎片，避免重复分配和释放内存带来的开销 。它也可以用于实现自定义的内存池或者嵌入式系统等场景 。当我们需要在已经分配好的特定内存创建对象时，placement new 是一种很方便的方式。当然，它也有一些缺点，比如需要手动调用析构函数和释放内存，以及可能造成指针混乱或者类型不匹配等问题。
{: .prompt-info}

   

样例：

假设我们有一个类 A，它有一个构造函数和一个析构函数：

```c++
class A {
public:
    A() {
        cout << "A constructor" << endl;
    }
    ~A() {
        cout << "A destructor" << endl;
    }
};
```

我们可以用 placement new 来在指定的内存空间中创建 A 的对象，而不需要再次分配内存。例如：

```c++
char *p = (char *)malloc(sizeof(A)); // 分配一个 A 大小的空间，并让指针 p 指向它
A *a = new(p) A(); // 在 p 指向的空间中创建一个 A 对象，并让指针 a 指向它
a->~A(); // 调用析构函数销毁对象 a 
free(p); // 释放指针 p 指向的空间
```

这段代码的输出是：

```c++
A constructor
A destructor
```

这说明我们成功地在已经分配好的内存空间中创建和销毁了 A 的对象。



​    

## 内存泄露

内存泄漏是指在程序中分配了内存，但没有及时释放，导致内存浪费的情况。为了避免内存泄漏，有以下几种方法：

- `尽量减少在程序中使用 new/delete 操作符`，最好不要使用。如果需要动态分配内存，可以使用 **RAII** 技术，即`在构造函数中分配内存，在析构函数中释放内存`。这样可以`保证对象离开作用域时自动释放内存`。

  > RAII 是一种 C++ 编程技术，它将资源的生命周期绑定到对象的生命周期。资源是指操作系统中有限的东西，如内存、文件、套接字等。对象是指存储在栈上的局部变量，它会在离开作用域时自动销毁。
  >
  > RAII 的原理是在构造函数中获取资源，在析构函数中释放资源。这样可以保证无论程序正常退出还是异常退出，都能正确地清理资源。例如：
  >
  > ```c++
  > class File {
  > public:
  >  File(const char* filename) {
  >      // 获取文件资源
  >      fp = fopen(filename, "r");
  >  }
  >  ~File() {
  >      // 释放文件资源
  >      if (fp) fclose(fp);
  >  }
  > private:
  >  FILE* fp;
  > };
  > 
  > void foo() {
  >  File f("test.txt"); // 在栈上创建一个 File 对象
  >  // ... 其他操作
  > } // 离开作用域时，f 的析构函数会自动调用，关闭文件、、
  > ```
  > RAII 的优点有：
  >
  > - 简化了资源管理的逻辑，不需要手动调用释放函数。
  > - 避免了资源泄漏，因为资源总是在对象销毁时自动释放。
  > - 提高了程序的安全性和可读性，因为不需要担心异常或跳转导致资源未释放。
  > - 支持[移动语义](../cpp-mobile-semantics/)，可以正确地转移资源的所有权。
  >
  > RAII 的缺点有：
  >
  > - 需要编写额外的类来封装资源，可能增加代码量和复杂度。
  > - 需要注意避免悬垂指针或多重释放的问题，特别是在使用智能指针时。
  {: .prompt-tip}

- `使用智能指针`（如 unique_ptr, shared_ptr 等）来管理动态分配的内存。智能指针可以自动跟踪引用计数，并在没有引用时释放内存。

- `使用标准库提供的容器（如 vector, string 等）来代替手动管理的数组或字符串`。标准库的容器会自动管理其大小和容量，并在不需要时释放内存。



   


## 写在最后

感谢你在茫茫人海中找到我🕵🏼

<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>

<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.3.1/css/all.css" integrity="sha384-mzrmE5qonljUremFsqc01SB46JvROS7bZs3IO2EmfFsd15uHvIt+Y8vEf7N7fWAU" crossorigin="anonymous">

<span id="busuanzi_container_page_pv">🎉你是第 <span id="busuanzi_value_page_pv"><i class="fa fa-spinner fa-spin"></i>  </span> 个读者

㊗️ 你平安喜乐，顺遂无忧！

希望你读完有所收获～

🥂🥂🥂 
