---
layout: post
title: C++ 移动语义
date: 2023-03-20 10:17 +0800
math: true
categories:
- 学习随笔
- C++
tags:
- C++
---



```c++
double mask_sum_errors = 0;
int mask_inlier_number = 0;
for (auto& v : contourErrors)
{
  float err = v.first * v.first * gamma;
  if (err < _norm_thr) {
    //cout << "contourErrors: " << (1 - err * _one_over_thr) * v.second << endl;
    mask_sum_errors -= (1 - err * _one_over_thr) * v.second;
    if (err < _threshold2)
      mask_inlier_number++;
  }
}

double prj_sum_errors = 0;
int prj_inlier_number = 0;
int nPoints = (int)_v3d.size();
for (int point = 0; point < nPoints; point++)
{
  double err = getError(_v3d[point], _v2d[point]) * gamma;
  if (err < _norm_thr) {
    prj_sum_errors -= (1 - err * _one_over_thr);
    if (err < _threshold2)
      prj_inlier_number++;
  }
  if (prj_sum_errors + mask_sum_errors - nPoints + point > this->_bestScore)
    break;
}

double sum_errors = mask_sum_errors + prj_sum_errors;
int inlier_number = mask_inlier_number + prj_inlier_number;
```

> 已知：contourErrors 存储的是当前采样3*3旋转矩阵R下的物体的轮廓点和真实旋转矩阵R'下物体的轮廓点之间的距离；\_v3d 是模型的三维点，\_v2d 是图像上的二维点，它们的元素是一一对应的，即 \_v3d[0] 的在图像的点是 \_v2d[0]；getError() 函数算的是在当前采样的旋转矩阵R下的重投影误差。
>
> 问题：我应该如何平衡两个误差，避免 sum_errors 或 inlier_number 被 mask/prj 给主导呢

## 写在最后

感谢你在茫茫人海中找到我🕵🏼

<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>

<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.3.1/css/all.css" integrity="sha384-mzrmE5qonljUremFsqc01SB46JvROS7bZs3IO2EmfFsd15uHvIt+Y8vEf7N7fWAU" crossorigin="anonymous">

<span id="busuanzi_container_page_pv">🎉你是第 <span id="busuanzi_value_page_pv"><i class="fa fa-spinner fa-spin"></i>  </span> 个读者

㊗️ 你平安喜乐，顺遂无忧！

希望你读完有所收获～

🥂🥂🥂 
