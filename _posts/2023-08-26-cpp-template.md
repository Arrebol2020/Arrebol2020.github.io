---
layout: post
title: c++ 中的template/模版
date: 2023-08-26 21:04 +0800
categories:
- 学习随笔
- C++
tags:
- C++
- template
---



C++ 模版是一种对类型进行参数化的工具。通常有两种形式：类模板和函数模板。使用模板可以`编写通用代码，不限于特定数据类型`



## 类模板

```cpp
template <typename T>
class MyTemplate
{
private:
		T data;
		
public:
		MyTemplate(int val) : data(val) {}
		
		T getValue() const
		{
				return data;
		}
}

int main()
{
  	MyTemplate<int> int_obj(5);
    MyTemplate<float> float_obj(3.14);
  
  	return 0;
}
```



## 函数模板

```cpp
template <typename T>
T max_value(T a, T b)
{
  	return (a > b) ? a : b;
}

int main()
{
  	int int_result = max_value(10, 20);
  	double double_result = max_value(3.14, 2.71);
  
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
