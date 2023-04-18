---
layout: post
title: 剑指 Offer 05. 替换空格
date: 2023-04-18 11:10 +0800
categories:
- 学习随笔
- 算法
tags:
- 剑指 Offer
---



## 题目描述

请实现一个函数，把字符串 `s` 中的每个空格替换成"%20"。

 

**示例 1：**

```bash
输入：s = "We are happy."
输出："We%20are%20happy."
```

 

**限制：**

```bash
0 <= s 的长度 <= 10000
```

   

## 解题

使用原地修改方法：

- 遍历字符串，统计空格数量
- 将字符串 resize 成替换空格后的长度
- `从后往前替换空格（从前往后会覆盖字符），i 指针指向原来字符串的末尾，j 指针指向替换后字符串的末尾，当 i 指针为空格时，进行替换，否则直接将 i 的字符赋值给 j `



```c++
class Solution {
public:
    string replaceSpace(string s) {
        int black_count = 0;
        int len = s.size();

        // 统计空格数量
        for ( int i = 0; i < len; i++ ) {
            if ( s[i] == ' ' ) black_count += 1;
        }

        // resize 字符串大小
        s.resize(s.size() + black_count * 2);

        // 从后往前替换空格
        for ( int i = len - 1, j = s.size() - 1; i < j; i--, j-- ) {
            if ( s[i] == ' ') {  // s[i] 为空格时，进行替换
                s[j] = '0';
                s[j - 1] = '2';
                s[j - 2] = '%';
                j -= 2;
            } else {  // 直接赋值
                s[j] = s[i];
            }
        }
        return s;
    }
};
```



复杂度分析：

- 时间复杂度 O(N) ： 遍历统计、遍历修改皆使用 O(N) 时间。

- 空间复杂度 O(1) ： 由于是原地扩展 s 长度，因此使用 O(1) 额外空间。

   

## 写在最后

感谢你在茫茫人海中找到我🕵🏼

<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>

<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.3.1/css/all.css" integrity="sha384-mzrmE5qonljUremFsqc01SB46JvROS7bZs3IO2EmfFsd15uHvIt+Y8vEf7N7fWAU" crossorigin="anonymous">

<span id="busuanzi_container_page_pv">🎉你是第 <span id="busuanzi_value_page_pv"><i class="fa fa-spinner fa-spin"></i>  </span> 个读者

㊗️ 你平安喜乐，顺遂无忧！

希望你读完有所收获～

🥂🥂🥂 
