---
layout: post
title: 剑指 Offer 11. 旋转数组的最小数字
date: 2023-04-18 11:10 +0800
categories:
- 学习随笔
- 算法
tags:
- 剑指 Offer
---



## 题目描述

把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。

给你一个可能存在 **重复** 元素值的数组 `numbers` ，它原来是一个升序排列的数组，并按上述情形进行了一次旋转。请返回旋转数组的**最小元素**。例如，数组 `[3,4,5,1,2]` 为 `[1,2,3,4,5]` 的一次旋转，该数组的最小值为 1。 

注意，数组 `[a[0], a[1], a[2], ..., a[n-1]]` 旋转一次 的结果为数组 `[a[n-1], a[0], a[1], a[2], ..., a[n-2]]` 。

 

**示例 1：**

```bash
输入：numbers = [3,4,5,1,2]
输出：1
```

**示例 2：**

```bash
输入：numbers = [2,2,2,0,1]
输出：0
```

 

**提示：**

- `n == numbers.length`
- `1 <= n <= 5000`
- `-5000 <= numbers[i] <= 5000`
- `numbers` 原来是一个升序排序的数组，并进行了 `1` 至 `n` 次旋转

## 解题

### 方法 1：

它只翻转了一次，且原来是省序数组

- 从下标 1 遍历到 numbers.size() - 2，如果遇到当前元素 < 后一个元素，则返回后一个元素（即翻转点）
- 遍历完没有返回，则说明没有移动，就是第一个元素



```c++
class Solution {
public:
    int minArray(vector<int>& numbers) {
        for ( int i = 0; i < numbers.size() - 1; i++ ) {
            if ( numbers[i] > numbers[i + 1] ) return numbers[i + 1]; 
        }

        return numbers[0];
    }
};
```



复杂度分析：

- 时间复杂度 O(N) ： 遍历数组，使用 O(N) 时间。

- 空间复杂度 O(1) ： 使用 O(1) 额外空间。  



### 方法 2：

二分法：https://leetcode.cn/problems/xuan-zhuan-shu-zu-de-zui-xiao-shu-zi-lcof/solutions/102826/mian-shi-ti-11-xuan-zhuan-shu-zu-de-zui-xiao-shu-3/

```c++
class Solution {
public:
    int minArray(vector<int>& numbers) {
        int l = 0, r = numbers.size() - 1;
        while ( l <= r ) {
            int mid = l + (r - l) / 2;
            if ( numbers[mid] > numbers[r] ) l = mid + 1;
            else if ( numbers[mid] < numbers[r] ) r = mid;
            else r = r - 1;
        }

        return numbers[l];
    }
};
```



复杂度分析：

- 时间复杂度 O(log2N) ：  在特例情况下（例如 [1,1,1,1] ），会退化到 O*(*N)。

- 空间复杂度 O(1) ： 使用 O(1) 额外空间。  




## 写在最后

感谢你在茫茫人海中找到我🕵🏼

<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>

<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.3.1/css/all.css" integrity="sha384-mzrmE5qonljUremFsqc01SB46JvROS7bZs3IO2EmfFsd15uHvIt+Y8vEf7N7fWAU" crossorigin="anonymous">

<span id="busuanzi_container_page_pv">🎉你是第 <span id="busuanzi_value_page_pv"><i class="fa fa-spinner fa-spin"></i>  </span> 个读者

㊗️ 你平安喜乐，顺遂无忧！

希望你读完有所收获～

🥂🥂🥂 
