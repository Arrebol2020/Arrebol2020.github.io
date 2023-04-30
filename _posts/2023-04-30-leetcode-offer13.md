---
layout: post
title: 剑指 Offer 13. 机器人的运动范围
date: 2023-04-30 21:16 +0800
categories:
- 学习随笔
- 算法
tags:
- 剑指 Offer
---



## 题目描述

地上有一个m行n列的方格，从坐标 `[0,0]` 到坐标 `[m-1,n-1]` 。一个机器人从坐标 `[0, 0] `的格子开始移动，它每次可以向左、右、上、下移动一格（不能移动到方格外），也不能进入行坐标和列坐标的数位之和大于k的格子。例如，当k为18时，机器人能够进入方格 [35, 37] ，因为3+5+3+7=18。但它不能进入方格 [35, 38]，因为3+5+3+8=19。请问该机器人能够到达多少个格子？

 

**示例 1：**

```bash
输入：m = 2, n = 3, k = 1
输出：3
```

**示例 2：**

```bash
输入：m = 3, n = 1, k = 0
输出：1
```

**提示：**

- `1 <= n,m <= 100`
- `0 <= k <= 20`

## 解题

每个点都往左移或往下移，直到移到边界或者访问过或者不符合条件

```c++
class Solution {
private:
    int rows, cols;
    vector<vector<bool>> visited;

    int sum_int(int n) {
        int sum = 0;
        while (n) {
            sum += n % 10;
            n /= 10;
        }

        return sum;
    }

    int backTrack(int i, int j, int k) {
        if ( i < 0 || i >= rows || j < 0 || j >= cols || visited[i][j] == true || sum_int(i) + sum_int(j) > k ) return 0;
        visited[i][j] = true;
        return 1 + backTrack(i + 1, j, k) + backTrack(i, j + 1, k);
    }
public:
    int movingCount(int m, int n, int k) {
        rows = m;
        cols = n;
        visited = vector<vector<bool>>(m, vector<bool>(n, false));
        
        return backTrack(0, 0, k);
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

🥂🥂🥂 
