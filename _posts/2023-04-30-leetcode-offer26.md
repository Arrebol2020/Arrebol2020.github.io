---
layout: post
title: 剑指 Offer 26. 树的子结构
date: 2023-04-30 21:10 +0800
categories:
- 学习随笔
- 算法
tags:
- 剑指 Offer
---



## 题目描述

输入两棵二叉树A和B，判断B是不是A的子结构。(约定空树不是任意一个树的子结构)

B是A的子结构， 即 A中有出现和B相同的结构和节点值。

例如:
给定的树 A:

`   3   / \   4  5  / \ 1  2`
给定的树 B：

`  4  / 1`
返回 true，因为 B 与 A 的一个子树拥有相同的结构和节点值。

**示例 1：**

```bash
输入：A = [1,2,3], B = [3,1]
输出：false
```

**示例 2：**

```bash
输入：A = [3,4,5,1,2], B = [4,1]
输出：true
```

**限制：**

```bash
0 <= 节点个数 <= 10000
```

## 解题

递归判断二叉树 A 是否包含 B 树，如果 B 不是 A 的子结构，就判断 B 是否是 A->left 或 A-> right 的子结构

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
private:
    bool recur(TreeNode* A, TreeNode* B) {
        if ( B == nullptr ) return true;  // 此时，当 B 位空时说明 B 是 A 的子结构
        if ( A == nullptr || A->val != B->val ) return false;  // A 为空 或者 当前两个结点不相等时说明不满足条件

        return recur(A->left, B->left) && recur(A->right, B->right);  // 继续判断左右结点是否相等
    }
public:
    bool isSubStructure(TreeNode* A, TreeNode* B) {
        if ( A == nullptr || B == nullptr ) return false;

        return recur(A, B) || isSubStructure(A->left, B) || isSubStructure(A->right, B);  // 只要其中一个满足即可
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
