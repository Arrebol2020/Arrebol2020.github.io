---
layout: post
title: 剑指 Offer 12. 矩阵中的路径
date: 2023-04-30 21:16 +0800
categories:
- 学习随笔
- 算法
tags:
- 剑指 Offer
---



## 题目描述

给定一个 `m x n` 二维字符网格 `board` 和一个字符串单词 `word` 。如果 `word` 存在于网格中，返回 `true` ；否则，返回 `false` 。

单词必须按照字母顺序，通过相邻的单元格内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母不允许被重复使用。

 

例如，在下面的 3×4 的矩阵中包含单词 "ABCCED"（单词中的字母已标出）。

![img](https://assets.leetcode.com/uploads/2020/11/04/word2.jpg)

 

**示例 1：**

```bash
输入：board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCCED"
输出：true
```

**示例 2：**

```bash
输入：board = [["a","b"],["c","d"]], word = "abcd"
输出：false
```

 

**提示：**

- `m == board.length`
- `n = board[i].length`
- `1 <= m, n <= 6`
- `1 <= word.length <= 15`
- `board `和` word `仅由大小写英文字母组成

## 解题

每个位置都可能是单词的起点，因此需要双重循环遍历 board 中的每个元素，只要某个位置能够组成单词，就返回 true。

如何判断 i j 位置是否能移动组成单词 word 呢？

就判断当前 i j 是否和当前位置的 word 字符相等，相等就让 i j 向 4 个方向移动，递归执行

```c++
class Solution {
private:
    int rows, cols;
    bool backTrack(vector<vector<char>>& board, int i, int j, int cur, string& word) {
        if ( i < 0 || i >= rows || j < 0 || j >= cols || board[i][j] != word[cur] ) return false;
        if ( cur == word.size() - 1 ) return true;

        board[i][j] = '\0';
        bool res = backTrack(board, i + 1, j, cur + 1, word) || backTrack(board, i - 1, j, cur + 1, word) || backTrack(board, i, j + 1, cur + 1, word) || backTrack(board, i, j - 1, cur + 1, word);
        board[i][j] = word[cur];

        return res;
    } 
public:
    bool exist(vector<vector<char>>& board, string word) {
        rows = board.size();
        cols = board[0].size();

        for ( int i = 0; i < rows; i++ ) {
            for ( int j = 0; j < cols; j++ ) {
                if ( backTrack(board, i, j, 0, word) ) return true;
            }
        }

        return false;
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
