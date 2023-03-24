---
layout: post
title: C++ STL 简述
date: 2023-03-24 16:58 +0800
categories:
- 学习随笔
- C++
tags:
- C++
- STL
---



STL 是 C++ 标准库的一部分，包含了诸多常用的基本数据结构和基本算法。STL包含了六个组件：`容器（Containers）、迭代器（Iterators）、算法（Algorithms）、函数对象（Function Objects）、适配器（Adapters）和空间配置器（Allocators）`。

- 容器是 `STL` 的核心，它是一种封装了数据存储和访问方式的数据结构，包括 `vector、list、deque、set、map 等等`。

  > 这些容器都有各自的特点和适用场景，例如 vector 适用于随机访问，list 适用于插入和删除，set 适用于去重，map 适用于键值对存储等等。
  {: .prompt-tip}

- 迭代器是一种抽象的概念，它提供了一种方法，使得我们可以依次访问容器中的每个元素，而不必关心容器内部的实现细节。

  > STL中的迭代器包括`输入迭代器（Input Iterator）、输出迭代器（Output Iterator）、前向迭代器（Forward Iterator）、双向迭代器（Bidirectional Iterator）和随机访问迭代器（Random Access Iterator）`。其中，输入迭代器和输出迭代器只能单向移动，前向迭代器可以向前移动，双向迭代器可以向前和向后移动，随机访问迭代器可以随机访问容器中的元素。
  {: .prompt-tip}

- 算法是 STL 的另一个重要组成部分，它是对容器中的元素进行操作的函数，包括排序、查找、删除、替换等等。

  > 这些算法都是对容器中的元素进行操作的函数，可以大大简化程序的编写。
  {: .prompt-tip}

- 函数对象是一种可调用的对象，它可以像函数一样被调用，是 STL 中的一种重要的编程技术。

  > 函数对象包括一元函数对象和二元函数对象，其中一元函数对象只有一个参数，二元函数对象有两个参数。STL中的函数对象包括 plus、minus、multiplies、divides、modulus、negate、equal_to、not_equal_to、greater、less、greater_equal、less_equal、logical_and、logical_or、logical_not 等等。
  {: .prompt-tip}

- 适配器是一种用来改变容器接口的机制，它可以将一种容器的接口转换成另一种容器的接口。

  > STL 中的适配器包括容器适配器、迭代器适配器和仿函数适配器。容器适配器包括 stack、queue 和 priority_queue，它们是对底层容器的封装，提供了不同的操作方式。迭代器适配器包括 reverse_iterator 和 insert_iterator，它们可以改变迭代器的行为。仿函数适配器包括 bind1st、bind2nd、not1、not2、ptr_fun、mem_fun和mem_fun_ref 等等，它们可以改变函数对象的行为。
  {: .prompt-tip}

- 空间配置器是STL中的另一个重要组成部分，它是一种用来管理内存的机制，可以自动地为容器分配和释放内存。

  > STL 中的空间配置器是最不需要介绍的，它总是藏在一切组件的背后，默默工作。整个 STL 的操作对象都存放在容器之中（vector、list），而容器一定需要配置空间以放置资料，这就是空间配置器的作用。STL中的空间配置器包括 SGI STL 标准配置器(std::allocator)、SGI STL 特殊配置器(__default_alloc_template)、SGI STL 第二级配置器(__malloc_alloc_template)等等。
  {: .prompt-tip}

​      

## 写在最后

感谢你在茫茫人海中找到我🕵🏼

<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>

<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.3.1/css/all.css" integrity="sha384-mzrmE5qonljUremFsqc01SB46JvROS7bZs3IO2EmfFsd15uHvIt+Y8vEf7N7fWAU" crossorigin="anonymous">

<span id="busuanzi_container_page_pv">🎉你是第 <span id="busuanzi_value_page_pv"><i class="fa fa-spinner fa-spin"></i>  </span> 个读者

㊗️ 你平安喜乐，顺遂无忧！

希望你读完有所收获～

🥂🥂🥂 
