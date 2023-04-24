---
layout: post
title: 剑指 Offer 53 - II. 0～n-1中缺失的数字
date: 2023-04-18 11:25 +0800
categories:
- 学习随笔
- 算法
tags:
- 剑指 Offer
---



## 题目描述

一个长度为n-1的递增排序数组中的所有数字都是唯一的，并且每个数字都在范围0～n-1之内。在范围0～n-1内的n个数字中有且只有一个数字不在该数组中，请找出这个数字。

 

**示例 1:**

```bash
输入: [0,1,3]
输出: 2
```

**示例 2:**

```bash
输入: [0,1,2,3,4,5,6,7,9]
输出: 8
```

 

**限制：**

```bash
1 <= 数组长度 <= 10000
```

 

## 解题

### 方法 1 

遍历数组，如果数组下标 i 与 nums[i] 不相等则下标 i 就是要找的值

遍历完成没有返回的话，则放回 nums.size() 

```c++
class Solution {
public:
    int missingNumber(vector<int>& nums) {
        for ( int i = 0; i < nums.size(); i++ ) {
            if ( i != nums[i] ) return i;
        }
        return nums.size();
    }
};
```



复杂度分析：

- 时间复杂度 O(N) ： 遍历数组时间为 O(N)

- 空间复杂度 O(1) ： 没有使用额外的存储

   

### 方法 2

二分法：https://leetcode.cn/problems/que-shi-de-shu-zi-lcof/solutions/155915/mian-shi-ti-53-ii-0n-1zhong-que-shi-de-shu-zi-er-f/

```c++
class Solution {
public:
    int missingNumber(vector<int>& nums) {
        int l = 0, r = nums.size() - 1;

        while ( l <= r ) {
            int mid = l + (r - l) / 2;
            if ( nums[mid] == mid ) l = mid + 1;
            else r = mid - 1;
        }

        return l;
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

