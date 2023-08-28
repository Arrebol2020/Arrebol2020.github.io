---
layout: post
title: c++ 中的 proority queue
date: 2023-08-28 21:08 +0800
categories:
- 学习随笔
- C++
tags:
- C++
- priority queue
---



C++ 中的优先级队列是一种数据可够，它按照指定的优先级对元素进行排序。默认情况下，优先级队列使用大顶堆来实现，也可以通过传入自定义的对比函数来改变排序方式



1. 头文件<queue>：定义了优先级队列模板类 `std::priority_queue`

2. 创建优先级队列

   ```cpp
   #include <queue>
   std::priority_queue<int> pq;
   ```

3. 插入元素，push()

   ```cpp
   pq.push(10);
   pq.push(20);
   pq.push(5);
   ```

4. 访问指顶部元素，top()

   ```cpp
   int top_element = pq.top();	
   ```

5. 删除顶部元素，pop()

   ```cpp
   pq.pop();
   ```

6. 自定义排序方式

   ```cpp
   std::priority_queue<int, std::vector<int>, std::greater<int>> pq;  // 小顶堆
   ```

   



## 写在最后

感谢你在茫茫人海中找到我🕵🏼

<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>

<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.3.1/css/all.css" integrity="sha384-mzrmE5qonljUremFsqc01SB46JvROS7bZs3IO2EmfFsd15uHvIt+Y8vEf7N7fWAU" crossorigin="anonymous">

<span id="busuanzi_container_page_pv">🎉你是第 <span id="busuanzi_value_page_pv"><i class="fa fa-spinner fa-spin"></i>  </span> 个读者

㊗️ 你平安喜乐，顺遂无忧！

希望你读完有所收获～

🥂🥂🥂 
