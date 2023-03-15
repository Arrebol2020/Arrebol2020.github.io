---
layout: post
title: C++ 虚函数
date: 2023-03-15 20:06 +0800
categories:
- 学习随笔
tags:
- C++
- 虚函数
- 纯虚函数
---



## 虚函数

C ++ 虚函数是一种实现多态的机制，它可以让基类的指针或引用调用派生类的成员函数。`虚函数的作用是实现运行时决议，即根据指针或引用所指向或绑定的对象的实际类型`，**动态的确定调用哪个函数**

定义虚函数的方法实在函数声明前加上关键字 virtual ：

```c++
class Animal{
public:
    virtual void makeSound();  // 虚函数
    virtual void makeSound = 0;
}
```

重写虚函数的方法是在派生类中使用相同的函数名和参数列表，并且可以加上关键字 `override` 来检查是否正确重写了基类的虚函数

> 如果定义成纯虚函数，则派生类必须使用 `override` 来实现，且基类不能被实例化
{: .prompt-danger}

```c++
class Dog : public Animal{
public:
    void makeSound() override;
}
```

调用虚函数的方法有三种：对象名、指针和引用

```c++
Animal a; // 基类对象
Dog d; // 派生类对象
Animal *pa = &a; // 基类指针指向基类对象
Animal *pd = &d; // 基类指针指向派生类对象

a.makeSound(); // 调用基类的makeSound()
d.makeSound(); // 调用派生类的makeSound()
pa->makeSound(); // 调用基类的makeSound()
pd->makeSound(); // 调用派生类的makeSound()
```

通过对象名调用虚函数时，会根据对象的类型确定调用哪个函数，这是静态关联。通过`指针或引用`调用虚函数时，会根据指针或引用所指向或绑定的对象确定调用哪个函数，这是动态关联。

为了实现动态关联，C++编译器会为每个含有虚函数的类生成一个虚函数表（virtual function table），并为每个该类的对象分配一个虚指针（virtual pointer），指向该表。虚函数表中存放了该类所有虚函数的地址，而虚指针则在运行时根据对象所属的类型进行更新。当通过指针或引用调用虚函数时，编译器会先通过该指针或引用找到对应对象的虚指针，然后通过该虚指针找到对应类的虚函数表，最后通过该表找到对应虚函数并执行



下面是一个例子：

```c++
#include <iostream>
using namespace std;

class Animal {
public:
    virtual void makeSound() { cout << "Animal sound" << endl; }
};

class Dog : public Animal {
public:
    void makeSound() override { cout << "Woof" << endl; }
};

int main() {
    Animal a;
    Dog d;
    Animal *pa = &a;
    Animal *pd = &d;

    cout << "sizeof(Animal) = " << sizeof(Animal) << endl;
    cout << "sizeof(Dog) = " << sizeof(Dog) << endl;

    a.makeSound();
    d.makeSound();
    pa->makeSound();
    pd->makeSound();

    return 0;
}
```



输出结果：

```c++
sizeof(Animal) = 8
sizeof(Dog) = 8
Animal sound
Woof
Animal sound
Woof
```



从输出结果可以看出，Animal 和 Dog 都占` 8 个字节`，其中 4 个字节是虚指针，另外 4 个字节是对象的其他数据（如果有的话）。虚指针在运行时会指向对象所属类的虚函数表，而虚函数表中存放了该类所有虚函数的地址 。

> 当通过对象名调用虚函数时，编译器会根据对象的类型确定调用哪个函数，这是静态关联。例如， a.makeSound() 会调用 Animal 类的 makeSound() 函数，而 d.makeSound() 会调用 Dog 类的 makeSound() 函数。
{: .prompt-tip}

当通过指针或引用调用虚函数时，编译器会先通过该指针或引用找到对应对象的虚指针，然后通过该虚指针找到对应类的虚函数表，最后通过该表找到对应虚函数并执行 。

> 例如，pa->makeSound() 会先通过 pa 找到 a 对象的虚指针，然后通过该虚指针找到 Animal 类的虚函数表，最后通过该表找到 Animal 类的 makeSound() 函数并执行。同理，pd->makeSound() 会先通过 pd 找到 d 对象的虚指针，然后通过该虚指针找到 Dog 类的虚函数表，最后通过该表找到 Dog 类的 makeSound() 函数并执行。
{: .prompt-info}



## 虚函数表如何找到对应的虚函数

虚函数表是一个存放虚函数地址的数组，每个虚函数在虚函数表中都有一个固定的位置，这个位置由编译器在编译时确定。当通过指针或引用调用虚函数时，编译器会根据指针或引用的类型和虚函数的名字和参数列表，计算出虚函数在虚函数表中的偏移量，然后通过该偏移量找到对应的虚函数地址，并执行该地址所指向的代码。例如，如果 Animal 类有两个虚函数 makeSound() 和 eat() ，并且 makeSound() 在虚函数表中的位置是 0，eat() 在虚函数表中的位置是 1 ，那么当通过 Animal* 类型的指针 pa 调用 pa->makeSound() 时，编译器会计算出 makeSound()  在虚函数表中的偏移量是 0，然后通过 pa 找到对象的虚指针，再通过该虚指针找到对象所属类的虚函数表，最后通过该表加上偏移量 0 找到对应的虚函数地址，并执行该地址所指向的代码。



## 虚函数与纯虚函数

虚函数和纯虚函数是 C++ 中的重要概念，它们都可以实现多态性，但是有一些区别：

- 虚函数是在基类中用 virtual 关键字声明的函数，它可以在派生类中被重写（override），从而实现动态绑定。虚函数既有定义，也有实现的代码。
- 纯虚函数是在基类中用 virtual 关键字声明，`并且在后面加上 =0 的函数`，它没有实现的代码，只是规定了派生类必须实现的接口。
- `包含纯虚函数的类叫做抽象类，它不能被实例化，只能作为基类`。 而包含虚函数的类可以被实例化，也可以作为基类。
- 纯虚函数可以有定义，但是必须在类外部定义，并且不能被调用。4而虚函数可以在类内部或外部定义，并且可以被调用。



样例：

```c++
#include <iostream>
using namespace std;

// 基类
class Animal {
public:
    // 虚函数
    virtual void eat() {
        cout << "Animal is eating." << endl;
    }
    // 纯虚函数
    virtual void speak() = 0;
};

// 派生类
class Dog : public Animal {
public:
    // 重写虚函数
    void eat() override {
        cout << "Dog is eating." << endl;
    }
    // 实现纯虚函数
    void speak() override {
        cout << "Dog is barking." << endl;
    }
};

int main() {
    // Animal a; // 错误，不能创建抽象类的对象
    Animal* p = new Dog(); // 正确，可以创建指向派生类的指针
    p->eat(); // 输出 Dog is eating.
    p->speak(); // 输出 Dog is barking.
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
