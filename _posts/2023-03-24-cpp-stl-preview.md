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

- 容器是 `STL` 的核心，它是一种封装了数据存储和访问方式的数据结构，包括 `vector、list、deque、set、map 等等`。是一种 class template。

  > 这些容器都有各自的特点和适用场景，例如 vector 适用于随机访问，list 适用于插入和删除，set 适用于去重，map 适用于键值对存储等等。
  {: .prompt-tip}

- 迭代器是一种抽象的概念，它提供了一种方法，使得我们可以依次访问容器中的每个元素，而不必关心容器内部的实现细节。是一种 class template。

  > STL中的迭代器包括`输入迭代器（Input Iterator）、输出迭代器（Output Iterator）、前向迭代器（Forward Iterator）、双向迭代器（Bidirectional Iterator）和随机访问迭代器（Random Access Iterator）`。其中，输入迭代器和输出迭代器只能单向移动，前向迭代器可以向前移动，双向迭代器可以向前和向后移动，随机访问迭代器可以随机访问容器中的元素。
  {: .prompt-tip}

- 算法是 STL 的另一个重要组成部分，它是对容器中的元素进行操作的函数，包括 sort、find、copy、delete、replace等等。是一种 function template。

  > 这些算法都是对容器中的元素进行操作的函数，可以大大简化程序的编写。
  {: .prompt-tip}

- 仿函数对象是一种可调用的对象，它可以像函数一样被调用，是 STL 中的一种重要的编程技术。是一种重载了 operator() 的 class 或者 class template。

  > 函数对象包括一元函数对象和二元函数对象，其中一元函数对象只有一个参数，二元函数对象有两个参数。STL中的函数对象包括 plus、minus、multiplies、divides、modulus、negate、equal_to、not_equal_to、greater、less、greater_equal、less_equal、logical_and、logical_or、logical_not 等等。
  {: .prompt-tip}

- 适配器是一种用来改变容器接口的机制，它可以将一种容器的接口转换成另一种容器的接口。

  > STL 中的适配器包括容器适配器、迭代器适配器和仿函数适配器。容器适配器包括 stack、queue 和 priority_queue，它们是对底层容器的封装，提供了不同的操作方式。迭代器适配器包括 reverse_iterator 和 insert_iterator，它们可以改变迭代器的行为。仿函数适配器包括 bind1st、bind2nd、not1、not2、ptr_fun、mem_fun和mem_fun_ref 等等，它们可以改变函数对象的行为。
  {: .prompt-tip}

- 空间配置器是 STL 中的另一个重要组成部分，它是一种用来管理内存的机制，可以自动地为容器分配和释放内存。是一种 class template。一般的分配器的 `std::alloctor` 都含有两个函数 allocate 与 deallocte，这两个函数分别调用了 `operator new()` 与 `delete()`，这两个函数的底层又分别是 `malloc()` 和 `free()`。

  > STL 中的空间配置器是最不需要介绍的，它总是藏在一切组件的背后，默默工作。整个 STL 的操作对象都存放在容器之中（vector、list），而容器一定需要配置空间以放置资料，这就是空间配置器的作用。STL中的空间配置器包括 SGI STL 标准配置器(std::allocator)、SGI STL 特殊配置器(__default_alloc_template)、SGI STL 第二级配置器(__malloc_alloc_template)等等。
  {: .prompt-tip}

​      

STL 六大组件的关系：

1. 容器通过空间配置器取得数据存储空间
2. 算法通过迭代器存储容器中的内容
3. 仿函数可以协助算法完成不同的策略的变化
4. 适配器可以修饰仿函数



## vector

Vector 在堆中分配了一段连续的内存空间来存放元素。有三个迭代器：

- first ：指向的是 vector 中对象的起始字节位置
- last ：指向当前队后一个对象的末尾字节
- end ：指向整个 vector 容器所占用内存空间的末尾字节



Vector 中的扩容：先分配一块更大的内存（通常是原来的两倍），将原来的数据复制过来，释放之前的内存，再插入新增的元素。



resize() ：改变当前容器内含有元素的数量 size() ，而不是容器的容量

1. 当 resize(len) 中 len > v.capacity() ，则数组中的 size 和 capacity 均设置为 len
2. 当 resize(len) 中 len <= v.capacity() ，则数组中的 size 设置为 len，而 capacity 不变

reserve()：改变当前容器的最⼤容量（capacity）

1. 如果 reserve(len) 的值 > 当前的 capacity()，那么会重新分配⼀块能存 len 个对象的空间，然后把之前的对象通过 copy construtor 复制过来，销毁之前的内存
2. 当r eserve(len) 中 len<= 当前的 capacity()，则数组中的 capacity 不变，size 不变，即不对容器做任何改变

   

##  list

每个元素都是放在⼀块内存中，它的内存空间可以是不连续的，通过指针来进⾏数据的访问。在哪⾥添加删除元素性能都很⾼，不需要移动内存，当然也不需要对每个元素都进⾏构造与析构，`常用来做随机插入和删除操作容器`。



vector 与 list 的区别：

1. vector 底层实现是数组；list 是双向链表
2. vector 时顺序内存，支持随机访问；list 不行
3. vector 在中间节点进行插入、删除会导致内存拷贝；list 不会
4. vector 一次性分配好内存，不够时才会进行翻倍扩容；list 每次插入新节点都会进行内存申请
5. vector 随机访问性能好，插入删除性能差；list 随机访问性能很差，插入删除性能好

   

## deque

deque 是一个双端开口的连续线性空间，其内部为分段连续的空间组成，随时可以增加一段新的空间并链接。支持快速随机访问，由于 deque 需要处理内部跳转，因此速度上没有 vector 快。

> 由于 deque 的迭代器⽐ vector 要复杂，这影响了各个运算层⾯，所以除⾮必要尽量使⽤ vector；为了提⾼效率，`在对 deque 进⾏排序操作的时候，我们可以先把 deque 复制到 vector 中再进⾏排序最后在复制回 deque`。
{: .prompt-tip}

   

## stack 和 queue

栈和队列被称为 deque 的配接器，其底层是以 deque 为底部架构。通过 deque 执行具体操作

   

## heap

建立在完全二叉树上，分为两种，大根堆，小根堆，其在 STL 中做 priority_queue（以任何顺序将元素推入容器中，然后取出时一定是从优先级最高的元素开始取，完全二叉树具有这样的性质，适合做 proority_queue 的底层） 的助手

   

## priority_queue

优先队列，也是配接器。其内的元素不是按照被推⼊的顺序排列，⽽是⾃动取元素的权值排列，缺省情况下利⽤⼀个 max-heap 完成。

   

## map 和 set

都是 C++ 的关联容器，只是通过它提供的接口对里面的元素进行访问，底层都是采用红黑树实现。

set：用来判断某一个元素是不是在一个组里面

map：映射，相当于词典，把一个值映射成另一个值，可以创建词典

查找一个数的时间为 O(logn) ；遍历时采用 iterator；但是每次插入值的时候，都需要调整个红黑树，效率有一定影响。



## map 和 unordered_map

map 中元素是一些 key-value 对，关键字起索引作用，值表示和索引相关的数据。map 底层是基于红黑树实现的，因此 `map 内部元素排列是有序的`；而 unordered_map 底层则是基于哈希表实现的，因此其元素的排列顺序是杂乱无序的。

map 的查找、删除、增加等一系列操作时间复杂度稳定，都为 O(logn)。unorderd_map 这些操作时间复杂度为 O(1)（极端可能为 O(N) ）。unordered_map 内部基于哈希表，空间占用率高

   

## 写在最后

感谢你在茫茫人海中找到我🕵🏼

<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>

<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.3.1/css/all.css" integrity="sha384-mzrmE5qonljUremFsqc01SB46JvROS7bZs3IO2EmfFsd15uHvIt+Y8vEf7N7fWAU" crossorigin="anonymous">

<span id="busuanzi_container_page_pv">🎉你是第 <span id="busuanzi_value_page_pv"><i class="fa fa-spinner fa-spin"></i>  </span> 个读者

㊗️ 你平安喜乐，顺遂无忧！

希望你读完有所收获～

🥂🥂🥂 
