---
layout: post
title: 剑指 Offer 59 - I. 滑动窗口的最大值
date: 2023-04-24 19:51 +0800
categories:
- 学习随笔
- 算法
tags:
- 剑指 Offer
---

## 题目描述

给定一个数组 `nums` 和滑动窗口的大小 `k`，请找出所有滑动窗口里的最大值。

**示例:**

```bash
输入: nums = [1,3,-1,-3,5,3,6,7], 和 k = 3
输出: [3,3,5,5,6,7] 
解释: 

  滑动窗口的位置                最大值
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7
```

 

**提示：**

你可以假设 *k* 总是有效的，在输入数组 **不为空** 的情况下，`1 ≤ k ≤ nums.length`。

## 解题

单调队列：

-  利用 deque 维护一个单调递减队列
- 队首为当前窗口的最大值
- push 时，保证插入的元素小于队尾元素（若大于，则循环弹出队尾元素）
- pop 时，如果 pop 的元素值时队首元素则弹出，否则不做任何处理

```c++
class Solution {
private:
    deque<int> dq;
    
    void dq_push(int val){
        // 维护单调栈
        while ( !dq.empty() && val > dq.back() ) {  // 这里不能等于，因为数组中可能有重复
            dq.pop_back();
        }
        dq.push_back(val);
    }

    void dq_pop(int val){
        // 只有在 val 和单调栈首相等时才弹出
        if ( !dq.empty() && val == dq.front() ) {
            dq.pop_front();
        }
    }

    int dq_max(){
        if( !dq.empty() ) return dq.front();
        else return -1;
    }
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        // 先初始化 窗口大小为 k 的单调栈
        for ( int i = 0; i < k; i++ ) {
            dq_push(nums[i]);
        }
        vector<int> res;
        res.push_back(dq_max());
        for ( int i = k; i < nums.size(); i++ ) {
            dq_pop(nums[i - k]);
            dq_push(nums[i]);
            res.push_back(dq_max());
        }

        return res;
    }
};
```



复杂度分析：

- 时间复杂度 O(N) ： 遍历数组使用 O(N) 时间。

- 空间复杂度 O(1) ： 常数量变量为 O(1)



## 写在最后

感谢你在茫茫人海中找到我🕵🏼

<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>

<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.3.1/css/all.css" integrity="sha384-mzrmE5qonljUremFsqc01SB46JvROS7bZs3IO2EmfFsd15uHvIt+Y8vEf7N7fWAU" crossorigin="anonymous">

<span id="busuanzi_container_page_pv">🎉你是第 <span id="busuanzi_value_page_pv"><i class="fa fa-spinner fa-spin"></i>  </span> 个读者

㊗️ 你平安喜乐，顺遂无忧！

希望你读完有所收获～

🥂🥂🥂 
