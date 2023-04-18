---
layout: post
title: 剑指 Offer 58 - II. 左旋转字符串
date: 2023-04-18 11:25 +0800
categories:
- 学习随笔
- 算法
tags:
- 剑指 Offer
---



## 题目描述

字符串的左旋转操作是把字符串前面的若干个字符转移到字符串的尾部。请定义一个函数实现字符串左旋转操作的功能。比如，输入字符串"abcdefg"和数字2，该函数将返回左旋转两位得到的结果"cdefgab"。

 

**示例 1：**

```bash
输入: s = "abcdefg", k = 2
输出: "cdefgab"
```

**示例 2：**

```bash
输入: s = "lrloseumgh", k = 6
输出: "umghlrlose"
```

 

**限制：**

- `1 <= k < s.length <= 10000`

   

## 解题

方法 1 ： 使用 reverse 方法

- 先 reverse 整个字符串
- 再 reverse 0 ~ s.size() - n 的字符串
- 最后 reverse s.size() - n ~ s.size() 的字符串



```c++
class Solution {
public:
    string reverseLeftWords(string s, int n) {
        reverse(s.begin(), s.end());
        reverse(s.begin(), s.begin() + s.size() - n);
        reverse(s.begin() + s.size() - n, s.end());

        return s;
    }
};
```



复杂度分析：

- 时间复杂度 O(N) ： reverse 的时间均为 O(N)

- 空间复杂度 O(1) ： 没有使用额外的存储

   

方法 2：使用拼接方法

- 创建一个 string 类型 res 变量
- 将 n~s.size()-1 字符串复制到 res 中
- 将 0~n-1 字符串复制到 res 中
- 返回 res



```c++
class Solution {
public:
    string reverseLeftWords(string s, int n) {
        string res;

        // 将 n~s.size()-1 字符串复制到 res 中
        for ( int i = n ; i < s.size(); i++ ) {
            res += s[i];
        }

        // 将 0~n-1 字符串复制到 res 中
        for ( int i = 0; i < n; i++ ) {
            res += s[i];
        }

        return res;
    }
};
```

   

## 写在最后

感谢你在茫茫人海中找到我🕵🏼

<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>

<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.3.1/css/all.css" integrity="sha384-mzrmE5qonljUremFsqc01SB46JvROS7bZs3IO2EmfFsd15uHvIt+Y8vEf7N7fWAU" crossorigin="anonymous">

<span id="busuanzi_container_page_pv">🎉你是第 <span id="busuanzi_value_page_pv"><i class="fa fa-spinner fa-spin"></i>  </span> 个读者

㊗️ 你平安喜乐，顺遂无忧！

希望你读完有所收获～

