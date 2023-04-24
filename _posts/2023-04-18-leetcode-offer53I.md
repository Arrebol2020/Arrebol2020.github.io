---
layout: post
title: 剑指 Offer 53 - I. 在排序数组中查找数字 I
date: 2023-04-18 19:28 +0800
categories:
- 学习随笔
- 算法
tags:
- 剑指 Offer
---



## 题目描述

统计一个数字在排序数组中出现的次数。

 

**示例 1:**

```bash
输入: nums = [5,7,7,8,8,10], target = 8
输出: 2
```

**示例 2:**

```bash
输入: nums = [5,7,7,8,8,10], target = 6
输出: 0
```

 

**提示：**

- `0 <= nums.length <= 105`
- `-109 <= nums[i] <= 109`
- `nums` 是一个非递减数组
- `-109 <= target <= 109`

## 解题

### 方法 1 

二分法找 target 和 target - 1 的`右边界`，taget 数量为 target 的右边界 - target - 1 的右边界

```c++
class Solution {
private:
    int binarySearch(vector<int>& nums, int target) {
        int l = 0, r = nums.size() - 1;
        while ( l <= r ) {
            int mid = l + (r - l) / 2;
            if ( nums[mid] <= target ) l = mid + 1;
            else r = mid - 1;
        }

        return l;
    }
public:
    int search(vector<int>& nums, int target) {
        return binarySearch(nums, target) - binarySearch(nums, target - 1);
    }
};
```



复杂度分析：

- 时间复杂度 O(log2N) ： 二分查找 N 个元素的数组，时间为 O(log2N)

- 空间复杂度 O(1) ： 没有使用额外的存储



### 方法 2

查找 target 的左右边界

```c++
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int l = 0, r = nums.size() - 1;
        int left = nums.size();
        while ( l <= r ) {
            int mid = l + (r - l) / 2;
            if ( nums[mid] < target ) l = mid + 1;
            else {
                r = mid - 1;
                left = mid;
            }
        }

        l = 0;
        r = nums.size() - 1;
        int right = nums.size();
        while ( l <= r ) {
            int mid = l + (r - l) / 2;
            if ( nums[mid] <= target ) {
                l = mid + 1;
                right = mid;
            } else r = mid - 1;
        }
        //right = right - 1;

        if ( left < nums.size() && right < nums.size() && nums[left] == target && nums[right] == target ) {
            return right - left + 1;
        }

        return 0;
    }
};
```

复杂度分析：

- 时间复杂度 O(log2N) ： 二分查找 N 个元素的数组，时间为 O(log2N)

- 空间复杂度 O(1) ： 没有使用额外的存储



## 写在最后

感谢你在茫茫人海中找到我🕵🏼

<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>

<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.3.1/css/all.css" integrity="sha384-mzrmE5qonljUremFsqc01SB46JvROS7bZs3IO2EmfFsd15uHvIt+Y8vEf7N7fWAU" crossorigin="anonymous">

<span id="busuanzi_container_page_pv">🎉你是第 <span id="busuanzi_value_page_pv"><i class="fa fa-spinner fa-spin"></i>  </span> 个读者

㊗️ 你平安喜乐，顺遂无忧！

希望你读完有所收获～
