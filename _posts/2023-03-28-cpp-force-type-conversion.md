---
layout: post
title: c++ 强制类型转化
date: 2023-03-28 10:21 +0800
categories:
- 学习随笔
- C++
tags:
- C++
- 类型转换
---



在 C++ 中，强制类型转换用于将一个数据类型的值转换为另一个数据类型。强制类型转换可以通过使用一组特殊的关键字来实现。以下是 C++ 中四种强制类型转换的方式：

- static_cast：在**编译时进行类型转换**，用于`基本数据类型之间的转换`和`具有继承关系的类之间的转换`。例如，可以将 int 转换为 double，或者`将指向基类对象的指针转换为指向派生类对象的指针`。使用时需要注意，静态转换的类型检查是在编译期间完成的，因此`需要确保转换的类型是安全的`。进行上行转换（把派生类指针或引用转换成基类表示）是安全的。进行下行转换（把基类的指针或引用转化为派生类表示），由于没有动态类型检查，所以是不安全的。

  ```c++
  int i = 100;
  double d = static_cast<double>(i); // 使用 static_cast 进行类型转换
  ```

- dynamic_cast：在**运行时进行类型转换**，用于`具有继承关系的类之间的转换`。它可以在指向基类对象的指针或引用上执行派生类对象的向下转换，并**在转换失败时返回 null 或抛出异常**。使用时需要注意，动态转换只能在运行期间进行类型检查，因此它比静态转换更慢，而且**只有在目标类型是一个具有虚函数的类时才能使用**。

  ```c++
  class Base {
  public:
      virtual void print() {
          cout << "Base class" << endl;
      }
  };
  
  class Derived : public Base {
  public:
      void print() override {
          cout << "Derived class" << endl;
      }
  };
  
  int main() {
      Base *b = new Derived();
      Derived *d = dynamic_cast<Derived*>(b); // 使用 dynamic_cast 进行类型转换，返回 Derived* 类型的指针
  
      if (d == nullptr) {
          cout << "Dynamic cast failed" << endl;
      } else {
          cout << "Dynamic cast succeeded" << endl;
          d->print(); // 输出 Derived class
      }
  
      return 0;
  }
  
  ```

  

- reinterpret_cast：在**编译时进行类型转换**，用于不同类型之间的位模式转换。例如，可以将指针转换为整数，或者将整数转换为指针。使用时需要注意，重新解释转换的结果往往是不可预测的，因此只有在确保转换是安全的情况下才能使用。

  ```c++
  #include <iostream>
  using namespace std;
  
  int main() {
      int x = 25;
  
      // 使用 reinterpret_cast 将 int 类型的变量 x 解释为 double 类型
      double y = reinterpret_cast<double&>(x);
  
      cout << "x: " << x << endl;
      cout << "y: " << y << endl;
  
      return 0;
  }
  ```

  输出：

  ```bash
  x: 25
  y: 2.12198e-314
  ```

  在这个例子中，我们使用 reinterpret_cast 将一个 int 类型的变量 x 解释为 double 类型的变量 y。由于 int 和 double 类型的大小和内部表示方式不同，因此在此过程中发生了数据类型的转换。注意，这种转换不是安全的，因为它会导致数据的精度和值的损失。

- const_cast：在编译时进行类型转换，用于删除常量性。例如，可以将指向常量对象的指针转换为指向非常量对象的指针。使用时需要注意，const_cast 应该只用于修改代码中的常量，而不是修改运行时数据的常量性。

  当一个变量被声明为 const，表示它的值不能被修改。但是，在某些情况下，我们可能需要修改这个值，例如在函数参数中声明为 const 时，但实际上我们需要修改这个参数。此时，就需要用到 const_cast。

  ```c++
  #include <iostream>
  using namespace std;
  
  int main() {
      const int a = 5;
      const int* ptr = &a;
  
      // 使用 const_cast 去除 ptr 指针的常量属性
      int* ptr2 = const_cast<int*>(ptr);
  
      // 通过 ptr2 指针修改 a 变量的值
      *ptr2 = 10;
  
      // 打印 a 的值，发现 a 的值已经被修改了
      cout << "a = " << a << endl;
  
      return 0;
  }
  
  ```

  在上面的代码中，首先定义了一个常量整数 `a`，并且定义一个指向 `a` 的常量指针 `ptr`。然后，使用 `const_cast` 去除了 `ptr` 指针的常量属性，并将其赋值给 `ptr2` 指针。通过 `ptr2` 指针修改了 `a` 变量的值为 `10`。最后，输出了 `a` 的值，发现其值已经被修改为 `10`。

  > 需要注意的是，const_cast 只能用于去除变量的常量属性，不能用于去除变量的易变属性。这是因为易变属性并不是被编译器限定的，而是由程序员自行约定的。
  {: .prompt-tip}



   

## 写在最后

感谢你在茫茫人海中找到我🕵🏼

<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>

<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.3.1/css/all.css" integrity="sha384-mzrmE5qonljUremFsqc01SB46JvROS7bZs3IO2EmfFsd15uHvIt+Y8vEf7N7fWAU" crossorigin="anonymous">

<span id="busuanzi_container_page_pv">🎉你是第 <span id="busuanzi_value_page_pv"><i class="fa fa-spinner fa-spin"></i>  </span> 个读者

㊗️ 你平安喜乐，顺遂无忧！

希望你读完有所收获～

🥂🥂🥂 
