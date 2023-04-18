---
layout: post
title: 剑指 Offer 67. 把字符串转换成整数
date: 2023-04-18 19:28 +0800
categories:
- 学习随笔
- 算法
tags:
- 剑指 Offer
---



## 题目描述

写一个函数 StrToInt，实现把字符串转换成整数这个功能。不能使用 atoi 或者其他类似的库函数。

首先，该函数会根据需要丢弃无用的开头空格字符，直到寻找到第一个非空格的字符为止。

当我们寻找到的第一个非空字符为正或者负号时，则将该符号与之后面尽可能多的连续数字组合起来，作为该整数的正负号；假如第一个非空字符是数字，则直接将其与之后连续的数字字符组合起来，形成整数。

该字符串除了有效的整数部分之后也可能会存在多余的字符，这些字符可以被忽略，它们对于函数不应该造成影响。

注意：假如该字符串中的第一个非空格字符不是一个有效整数字符、字符串为空或字符串仅包含空白字符时，则你的函数不需要进行转换。

在任何情况下，若函数不能进行有效的转换时，请返回 0。

**说明：**

假设我们的环境只能存储 32 位大小的有符号整数，那么其数值范围为 [−231, 231 − 1]。如果数值超过这个范围，请返回  INT_MAX (231 − 1) 或 INT_MIN (−231) 。

**示例 1:**

```bash
输入: "42"
输出: 42
```

**示例 2:**

```bash
输入: "   -42"
输出: -42
解释: 第一个非空白字符为 '-', 它是一个负号。
     我们尽可能将负号与后面所有连续出现的数字组合起来，最后得到 -42 。
```

**示例 3:**

```bash
输入: "4193 with words"
输出: 4193
解释: 转换截止于数字 '3' ，因为它的下一个字符不为数字。
```

**示例 4:**

```bash
输入: "words and 987"
输出: 0
解释: 第一个非空字符是 'w', 但它不是数字或正、负号。
     因此无法执行有效的转换。
```

**示例 5:**

```bash
输入: "-91283472332"
输出: -2147483648
解释: 数字 "-91283472332" 超过 32 位有符号整数范围。 
     因此返回 INT_MIN (−231) 。
```

​    

## 解题

- 先过滤字符串前面的空格
- 判断时正数还是负数
- 判断当前的值是否越界，越界（ res * 10 > border || res == border && str[pos] > '7'）则根据正负数返回 INT_MAX 或 INT_MIN
- 根据正负数返回正数或负数



```c++
class Solution {
public:
    int strToInt(string str) {
        int pos = 0;

        // 移除空格
        while ( pos < str.size() && str[pos] == ' ') pos++;

        bool sign = true;
        // 记录正负数
        if ( str[pos] == '-' ) {
            pos++;
            sign = false;
        } else if ( str[pos] == '+' ) pos++;

        // 
        int res = 0;
        int border = INT_MAX / 10;
        while ( pos < str.size() ) {
            // 处理字母
            if ( str[pos] < '0' || str[pos] > '9' ) break;
            // 处理越界
            if ( res > border || res == border && str[pos] > '7' ) return sign == true ? INT_MAX : INT_MIN;

            res = res * 10 + (str[pos] - '0');
            pos++;
        }

        return sign == true ? res : -res;
    }
};
```



复杂度分析：

- 时间复杂度 O(N) ： 遍历长度为 N 的字符串

- 空间复杂度 O(1) ： 没有使用额外的存储

   



## 写在最后

感谢你在茫茫人海中找到我🕵🏼

<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>

<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.3.1/css/all.css" integrity="sha384-mzrmE5qonljUremFsqc01SB46JvROS7bZs3IO2EmfFsd15uHvIt+Y8vEf7N7fWAU" crossorigin="anonymous">

<span id="busuanzi_container_page_pv">🎉你是第 <span id="busuanzi_value_page_pv"><i class="fa fa-spinner fa-spin"></i>  </span> 个读者

㊗️ 你平安喜乐，顺遂无忧！

希望你读完有所收获～
