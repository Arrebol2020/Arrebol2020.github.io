---
layout: post
title: Cherno C++ 笔记
date: 2023-03-01 09:11 +0800
categories:
- 课程
- Cherno C++
tags:
- C++ 基础
---



`#` 之后的都是预处理语句（在实际编译发生之前就被处理）

main 函数不一定需要返回值，如果你不返回值，默认返回 0 ，这个只适用于 main 函数



如何将源代码变成可运行的二进制文件：

- #include <iostream> ：预处理语句，在编译文件之前会先进行求值，它会吧 iostream 文件的所有内容都放入该文件中
- 检查语法是否有误
- 检查语法无误后编译器将所有的 C++ 代码转化为实际的机器代码



 项目中的每一个 CPP 文件都会被编译，但是头文件不会被编译，因为头文件的内容在预处理时就被包含了进来，CPP 文件被编译的时候，包含进来的文件就一起被编译了。每一个 CPP 文件都会被编译成一个 object file （目标文件），链接器将会把每个 object file 合并成一个执行文件



当构建整个工程时，所有 CPP 文件都会被编译，链接器将会找到声明的函数的定义在哪里，将函数定义导入到函数声明中，如果声明了函数，但是链接器找不到相关的函数定义，将会出现链接错误

unresolved external symbol 无法解析的外部符号：链接器无法解析这个符号，链接器的工作是链接函数，如果找不到声明的函数的定义，就会出现这个错误



编译生成目标文件预处理代码，所有的预处理器语句都会处理，一旦代码被预处理，接下来将会或多或少进行记号化和解析，将 C++ 语言整理成编译器能够真正理解和推理的格式

- 在预处理阶段的处理，编译器基本上会遍历所有的预处理语句并对其进行处理，常用的预处理语句是 include（预处理打开 include 的那个文件，将文件的所有内容粘贴到你写的文件中） 、 if （可以让我们包含或者排除基于给定条件的代码）、ifdef 、 pragma 、define （基本上只进行搜索 中间的内容 替换成 后面的内容）。它告诉编译器到底要做什么

链接的主要焦点是找到每个符号和函数在哪里，入口点不一定是 main 函数，通常它是 main 函数



变量的类型大小与它能存储多大的数字直接相关。unsigned 定义了一个没有符号位的数据类型

bool 数据类型占用一个字节的内存，为什么不用 1 个 bit 来表示？bool 确实只需要一个比特来表示，然而，当我们处理寻址内存时，也就是说，我们需要从内存中找回我们的 bool 变量的值，我们没有办法去寻址只有一个 bit 位的内容，我们只能寻址字节，因此，我们不能创建只有 1 个 bit 位的变量，因为我们需要能够访问它。但是你可以在一个 byte 内存存储 8 个 bool 值



要适当使用函数，若太频繁使用函数，会使得代码看起来凌乱不堪，不好维护，会让程序变慢。因为每次我们调用函数时，编译器生成一个 call 指令，这意味着，在一个运行的程序中，为了调用一个函数，我们需要创建一个堆栈结构，因此必须把像参数这样的东西推进堆栈，此外还需要将返回地址压入堆栈，随后跳到二进制执行文件的不同部分，以便开始执行我们的函数指令，为了将 push 进行的结果返回，然后我们得回去到最初调用函数之前，就像在内存中跳跃来执行函数，跳跃和执行都需要时间，所以它会减慢程序运行速度

函数的主要目的是防止代码重复



#pragma once 阻止我们单个头文件多次被 include ，并转化为单个翻译单元

include 有时候用 <> 有使用用 ""

- <> ：当我们知道 include 文件的路径时候（在某个文件夹），就可以使用这个来告诉编译器搜索包含路径文件夹
- ""：通常用于 include 相对于当前文件的文件
- <> 只用于编译器包含路径，"" 使用更随意

如何区分是 c++ 标准库还是 c 标准库：看 include 的头文件`是否带有 .h 后缀` ，带 .h 的是 c 标准库头文件，不带的则是 c++ 标准库头文件



## 分支语句

else if 不是 C++ 的关键字，是先 else 然后在 if

尽量避免使用 if 判断，尝试使用通过数学运算来达到 if 的效果



## 指针

一个指针就是一个地址，是一个在内存中保存地址的整数，它不需要类型，如果我们给指针一个类型，这表明我们认为这个地址的数据，是我们所假设的类型。类型无关紧要，但类型对该内存的操作很有用



## 引用

引用本身并不是新的变量，因此并不占用内存，没有真正的存储空间。指针就像引用（指针更加强大，更有用），然而`如果能使用引用完成的操作，一定用引用`，它会让代码更加简洁和简单。关于引用，`一旦你声明了一个引用，你不能改变它引用的东西`，eg：

```c++
int a = 5;
int b = 8;

int& ref = a;
ref = b;  // 这一行表示将 b 的值赋值给 a，而不是 ref 变成 b 的引用
```



当你声明一个引用时，你必须马上给它赋值，它不是一个真正的变量



## C++ 类

类只是对数据和功能组合在一起的一种方法（有数据和处理这些数据的函数 ）。



>  与 struct 的区别

`在 C++ 除了可见性以外没有区别，类成员默认为 private（只有类中的函数才能访问这些变量），结构体成员默认为 public`。 C++ 保留下来是为了和 C 兼容，可以通过

```c++
#define struct class
```

然后把 struct 中的变量设置成 public 



## Visual Studio 快捷键

ctrl + F7：单独编译某个 CPP 文件

修改 exe 的生成路径：在 `All Configurations` 和 `All Platforms` 下，将 `Output Directory` 配置成：

```
$(SolutionDir)bin\$(Platform)\$(Configuration)\
```

将 `Intermediate Directory` 配置成：

```
$(SolutionDir)bin\intermediates\$(Platform)\$(Configuration)\
```
