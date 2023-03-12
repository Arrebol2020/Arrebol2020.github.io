---
layout: post
title: Leetcode 算法 1 [10题]
date: 2023-03-11 20:11 +0800
categories:
- 算法
- LeetCode
tags:
- 二叉树
- 二叉搜索树
---



## 669. 修剪二叉搜索树

[力扣题目链接(opens new window)](https://leetcode.cn/problems/trim-a-binary-search-tree/)

给你二叉搜索树的根节点 `root` ，同时给定最小边界`low` 和最大边界 `high`。通过修剪二叉搜索树，使得所有节点的值在`[low, high]`中。修剪树 **不应该** 改变保留在树中的元素的相对结构 (即，如果没有被移除，原有的父代子代关系都应当保留)。 可以证明，存在 **唯一的答案** 。

所以结果应当返回修剪好的二叉搜索树的新的根节点。注意，根节点可能会根据给定的边界发生改变。

**示例 1：**

![img](https://assets.leetcode.com/uploads/2020/09/09/trim1.jpg)

```vb
输入：root = [1,0,2], low = 1, high = 2
输出：[1,null,2]
```



**示例 2：**

![img](https://assets.leetcode.com/uploads/2020/09/09/trim2.jpg)

```vb
输入：root = [3,0,4,null,2,null,null,1], low = 1, high = 3
输出：[3,2,null,1]
```



### 易错与思路

关键思路：

- 利用搜索二叉树性质

- 单层递归逻辑，有五种情况：

  - 没找到删除节点，遍历为空

  - 当前节点的左右孩子均为空，则返回 nullptr
  - 左孩子为空，右孩子不为空，返回 root->right
  - 右孩子为空，左孩子不为空，返回 root->left
  - 左右孩子都不为空
    - 先找到 root->right 的最左边节点 rightleft
    - 将 root->left 赋值给 rightleft->left
    - 将 root->right 赋值给 root



## 669. 修剪二叉搜索树

[力扣题目链接(opens new window)](https://leetcode.cn/problems/trim-a-binary-search-tree/)

给你二叉搜索树的根节点 `root` ，同时给定最小边界`low` 和最大边界 `high`。通过修剪二叉搜索树，使得所有节点的值在`[low, high]`中。修剪树 **不应该** 改变保留在树中的元素的相对结构 (即，如果没有被移除，原有的父代子代关系都应当保留)。 可以证明，存在 **唯一的答案** 。

所以结果应当返回修剪好的二叉搜索树的新的根节点。注意，根节点可能会根据给定的边界发生改变。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2020/09/09/trim1.jpg)

```vb
输入：root = [1,0,2], low = 1, high = 2
输出：[1,null,2]
```



**示例 2：**

![img](https://assets.leetcode.com/uploads/2020/09/09/trim2.jpg)

```vb
输入：root = [3,0,4,null,2,null,null,1], low = 1, high = 3
输出：[3,2,null,1]
```

 

### 易错与思路

易错的点：假设区间为 [7, 8]，当前节点值为 4，不能直接删除，因为`当前节点的右子树可能比 4 大，可能在区间 [7, 8] 中`，因此不能直接删除。同理，若当前节点值为 10，其左子树可能存在值小于 10，在区间 [7, 8] 中



## 108.将有序数组转换为二叉搜索树

[力扣题目链接(opens new window)](https://leetcode.cn/problems/convert-sorted-array-to-binary-search-tree/)

给你一个整数数组 `nums` ，其中元素已经按 **升序** 排列，请你将其转换为一棵 **高度平衡** 二叉搜索树。

**高度平衡** 二叉树是一棵满足「每个节点的左右两个子树的高度差的绝对值不超过 1 」的二叉树。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2021/02/18/btree1.jpg)

```vb
输入：nums = [-10,-3,0,5,9]
输出：[0,-3,9,-10,null,5]
解释：[0,-10,5,null,-3,null,9] 也将被视为正确答案：
```

**示例 2：**

![img](https://assets.leetcode.com/uploads/2021/02/18/btree.jpg)

```vb
输入：nums = [1,3]
输出：[3,1]
解释：[1,null,3] 和 [3,1] 都是高度平衡二叉搜索树。
```



### 易错与思路

关键思路：从数组中间位置取值作为节点元素，mid 计算需要注意数值越界 `mid = left + ((right - left) / 2);`



## 538.把二叉搜索树转换为累加树

[力扣题目链接(opens new window)](https://leetcode.cn/problems/convert-bst-to-greater-tree/)

给出二叉 **搜索** 树的根节点，该树的节点值各不相同，请你将其转换为累加树（Greater Sum Tree），使每个节点 `node` 的新值等于原树中大于或等于 `node.val` 的值之和。

提醒一下，二叉搜索树满足下列约束条件：

- 节点的左子树仅包含键 **小于** 节点键的节点。
- 节点的右子树仅包含键 **大于** 节点键的节点。
- 左右子树也必须是二叉搜索树。

**注意：**本题和 1038: https://leetcode-cn.com/problems/binary-search-tree-to-greater-sum-tree/ 相同

 

**示例 1：**

**![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2019/05/03/tree.png)**

```vb
输入：[4,1,6,0,2,5,7,null,null,null,3,null,null,null,8]
输出：[30,36,21,36,35,26,15,null,null,null,33,null,null,null,8]
```



**示例 2：**

```vb
输入：root = [0,null,1]
输出：[1,null,1]
```



### 易错与思路

关键思路：采用 `右中左` 遍历，用 pre 节点记录上一个节点的 val ，对 cur->val 进行累加



## 参考

- [代码随想录](https://programmercarl.com)
