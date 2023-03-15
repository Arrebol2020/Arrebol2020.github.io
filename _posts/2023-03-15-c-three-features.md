---
layout: post
title: C++【封装】【继承】与【多态】
date: 2023-03-15 21:25 +0800
categories:
- 学习随笔
- C++
tags:
- C++
- 封装
- 继承
- 多态
---



## 封装

C++ 封装是一种将数据和操作数据的函数绑定在一起的机制，也是面向对象编程的三大特性之一。C++ 封装的本质是通过类来实现的，类可以将数据和行为作为一个整体，表现生活中的事物。C++ 封装还可以通过访问权限控制（public、protected、private）来实现数据隐藏，即对外提供接口，对内屏蔽细节。

下面是一个简单的代码示例：

```c++
// 定义一个圆类
class Circle
{
public: // 公共访问权限
    // 属性
    int m_r; // 半径

    // 行为
    // 求圆的周长
    double calculate()
    {
        return 2 * 3.14 * m_r;
    }
};

int main()
{
    // 通过圆类，创建具体的圆（对象）
    Circle c1;
    // 给c1对象的属性进行赋值
    c1.m_r = 10;
    // 调用对象的行为
    cout << "圆的周长为：" << c1.calculate() << endl;

    return 0;
}
```

这个代码示例展示了如何通过类来封装数据和操作数据的函数，以及如何通过对象来访问类中的属性和行为。你可以看到，类中有一个公共访问权限的属性 m_r，表示圆的半径，以及一个公共访问权限的函数 calculate，表示计算圆的周长。在 main 函数中，我们通过 Circle 类创建了一个具体的圆对象 c1，并给它赋值了半径为 10。然后我们调用了 c1 对象的 calculate 函数，输出了圆的周长。



## 继承

C++ 继承是一种面向对象编程的特性，它允许一个类（派生类）继承另一个类（基类）的数据和函数，从而实现代码的重用和扩展。C++ 有三种继承方式：public（公有）、protected（保护）和private（私有），它们决定了基类成员在派生类中的访问权限。



下面是一个简单的代码示例：

```c++
// 基类
class Animal
{
public:
    void eat()
    {
        cout << "Animal is eating." << endl;
    }
};

// 派生类
class Dog : public Animal // 公有继承
{
public:
    void bark()
    {
        cout << "Dog is barking." << endl;
    }
};

int main()
{
    Dog d;
    d.eat(); // 调用基类的公有成员函数
    d.bark(); // 调用派生类的公有成员函数
    return 0;
}
```

这个代码示例展示了如何通过公有继承来创建一个派生类Dog，它继承了基类Animal的公有成员函数eat，并且在自己的类中定义了一个新的公有成员函数bark。在main函数中，我们可以通过创建一个Dog对象d，来调用它所继承或定义的公有成员函数。



## 多态

C++ 多态是指在不同的情况下，同一个函数名或者操作符可以有不同的行为。

 C++ 中有两种多态：

- 静态多态：在编译阶段实现的，它是通过函数重载或者运算符重载来实现的。 例如，你可以定义两个同名但参数不同的函数，根据传入的实参来调用相应的函数。
- 动态多态：在运行时实现的，它是通过虚函数和继承来实现的。例如，你可以定义一个基类和一个派生类，它们都有一个虚函数，然后用基类的指针或引用来调用该虚函数，根据指针或引用所指向的对象的类型来执行不同的函数。



下面是一个简单的代码示例：

静态多态

```c++
// 静态多态
// 函数重载
void print(int x) {
    cout << "int: " << x << endl;
}

void print(double x) {
    cout << "double: " << x << endl;
}

int main() {
    print(10); // 输出 int: 10
    print(3.14); // 输出 double: 3.14
    return 0;
}
```



动态多态：

```c++
// 动态多态
// 虚函数
class Shape {
public:
    // 虚函数
    virtual void draw() {
        cout << "Shape is drawing." << endl;
    }
};

class Circle : public Shape {
public:
    // 重写虚函数
    void draw() override {
        cout << "Circle is drawing." << endl;
    }
};

int main() {
    Shape* p = new Circle(); // 基类指针指向派生类对象
    p->draw(); // 输出 Circle is drawing.
    delete p;
    return 0;
}
```



## 写在最后

感谢你在茫茫人海中找到我🕵🏼

<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>

<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.3.1/css/all.css" integrity="sha384-mzrmE5qonljUremFsqc01SB46JvROS7bZs3IO2EmfFsd15uHvIt+Y8vEf7N7fWAU" crossorigin="anonymous">

<span id="busuanzi_container_page_pv">🎉你是第 <span id="busuanzi_value_page_pv"><i class="fa fa-spinner fa-spin"></i>  </span> 个读者

㊗️ 你平安喜乐，顺遂无忧！

希望你读完有所收获～

🥂🥂🥂 
