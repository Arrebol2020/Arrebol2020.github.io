---
layout: post
title: 剑指 Offer 63. 股票的最大利润
date: 2023-04-19 11:09 +0800
categories:
- 学习随笔
- 算法
tags:
- 剑指 Offer
---



## 题目描述

假设把某股票的价格按照时间先后顺序存储在数组中，请问买卖该股票一次可能获得的最大利润是多少？

 

**示例 1:**

```bash
输入: [7,1,5,3,6,4]
输出: 5
解释: 在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
     注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格。
```

**示例 2:**

```bash
输入: [7,6,4,3,1]
输出: 0
解释: 在这种情况下, 没有交易完成, 所以最大利润为 0。
```

 

**限制：**

```bash
0 <= 数组长度 <= 10^5
```



## 解题

### 方法 1

双指针：

- i 指向最佳买入时间，j 指向最佳卖出时间

- 当 prices[i] > prices[j] 更新 i，j 的值（i 向后移动 j - i ，j 向后移动 1）
- 否则，计算更新 res（prices[j] - prices[i] 和 res 的最大值），j 向后移动 1                                                                                                              

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        if ( prices.size() <= 1 ) return 0;
        int i = 0;  // 最佳买入
        int j = 1;  // 最佳卖出
        int res = 0;
        while ( j < prices.size() ) {
            if ( prices[i] > prices[j] ) {
                i += j - i;  // 因为 i-j 之间，全是比 prices[i] 大，直接将 i 移动 j - i 下
                j += 1;
            } else {
                res = max(res, prices[j] - prices[i]);
                j += 1;
            }
        }

        return res;
    }
};
```



复杂度分析：

- 时间复杂度 O(N) ： 遍历数组使用 O(N) 时间。

- 空间复杂度 O(1) ： 常数量变量为 O(1)



### 方法 2

动态规划：https://leetcode.cn/problems/gu-piao-de-zui-da-li-run-lcof/solutions/192221/mian-shi-ti-63-gu-piao-de-zui-da-li-run-dong-tai-2/

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int max_profit = 0, cost = INT_MAX;

        for ( int i = 0; i < prices.size(); i++ ) {
            cost = min(prices[i], cost);
            max_profit = max(max_profit, prices[i] - cost);
        }

        return max_profit;
    }
};
```



复杂度分析：

- 时间复杂度 O(N) ： 其中 N 为 prices 列表长度，动态规划需遍历 prices。
- 空间复杂度 O(1)： 变量 cost 和 profit 使用常数大小的额外空间。



## 写在最后

感谢你在茫茫人海中找到我🕵🏼

<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>

<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.3.1/css/all.css" integrity="sha384-mzrmE5qonljUremFsqc01SB46JvROS7bZs3IO2EmfFsd15uHvIt+Y8vEf7N7fWAU" crossorigin="anonymous">

<span id="busuanzi_container_page_pv">🎉你是第 <span id="busuanzi_value_page_pv"><i class="fa fa-spinner fa-spin"></i>  </span> 个读者

㊗️ 你平安喜乐，顺遂无忧！

希望你读完有所收获～

🥂🥂🥂 
