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
- 回溯
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



## 39. 组合总和

[力扣题目链接](https://leetcode.cn/problems/combination-sum/)

给你一个 **无重复元素** 的整数数组 `candidates` 和一个目标整数 `target` ，找出 `candidates` 中可以使数字和为目标数 `target` 的 所有 **不同组合** ，并以列表形式返回。你可以按 **任意顺序** 返回这些组合。

`candidates` 中的 **同一个** 数字可以 **无限制重复被选取** 。如果至少一个数字的被选数量不同，则两种组合是不同的。 

对于给定的输入，保证和为 `target` 的不同组合数少于 `150` 个。

 

**示例 1：**

```vb
输入：candidates = [2,3,6,7], target = 7
输出：[[2,2,3],[7]]
解释：
2 和 3 可以形成一组候选，2 + 2 + 3 = 7 。注意 2 可以使用多次。
7 也是一个候选， 7 = 7 。
仅有这两种组合。
```

**示例 2：**

```vb
输入: candidates = [2,3,5], target = 8
输出: [[2,2,2,2],[2,3,3],[3,5]]
```

**示例 3：**

```vb
输入: candidates = [2], target = 1
输出: []
```



### 易错与思路

> 什么时候需要 startIndex

如果是一个集合来求`组合`的话，就需要 startIndex

如果是多个集合求`组合`，各个集合之间互相不影响，那么就不要 startIndex



最外层的循环不是 i = 0，而是 startIndex，如果是 i = 0，就会出现：1 2 2 和 2 1 2 的结果



## 40.组合总和II

[力扣题目链接](https://leetcode.cn/problems/combination-sum-ii/)

给定一个候选人编号的集合 `candidates` 和一个目标数 `target` ，找出 `candidates` 中所有可以使数字和为 `target` 的组合。

`candidates` 中的每个数字在每个组合中只能使用 **一次** 。

**注意：**解集不能包含重复的组合。 

 

**示例 1:**

```vb
输入: candidates = [10,1,2,7,6,1,5], target = 8,
输出:
[
[1,1,6],
[1,2,5],
[1,7],
[2,6]
]
```

**示例 2:**

```vb
输入: candidates = [2,5,2,1,2], target = 5,
输出:
[
[1,2,2],
[5]
]
```



### 易错与思路

**集合（数组 candidates ）有重复元素，但还不能有重复的组合**。先对 candidates 进行排序，然后使用 used 数组来进行去重。

当 `i > 0 && candidates[i] == candidates[i-1] && used[i-1] == false` 时 continue



## 参考

- [代码随想录](https://programmercarl.com)



## 写在最后

感谢你在茫茫人海中找到我🕵🏼

<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>

<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.3.1/css/all.css" integrity="sha384-mzrmE5qonljUremFsqc01SB46JvROS7bZs3IO2EmfFsd15uHvIt+Y8vEf7N7fWAU" crossorigin="anonymous">

<span id="busuanzi_container_page_pv">🎉你是第 <span id="busuanzi_value_page_pv"><i class="fa fa-spinner fa-spin"></i>  </span> 个读者

㊗️ 你平安喜乐，顺遂无忧！

希望你读完有所收获～

🥂🥂🥂 
