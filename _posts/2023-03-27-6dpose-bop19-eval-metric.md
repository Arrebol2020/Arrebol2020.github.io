---
layout: post
title: 位姿估计 BOP19 评估指标
date: 2023-03-27 15:07 +0800
math: true
categories:
- 项目
tags:
- vsd
- mssd
- mspd
---



## 先验知识

在三维计算机视觉中，通常会将 3D 物体表示为一组由三维点坐标组成的点云，每个点由三个坐标 $(x, y, z)$ 表示。为了将 3D 点从世界坐标系转换为相机坐标系，需要使用旋转矩阵 $R$ 和平移向量 $T$ 进行变换，其变换公式为：

$$P_{camera} = RP_{world} + T$$

其中 $P_{world}$ 是 3D 点在世界坐标系下的坐标，$P_{camera}$ 是3D点在相机坐标系下的坐标。这个变换可以将相机坐标系的原点变换到世界坐标系中的 $T$ 点，并将相机坐标系沿 $x$ 轴、$y$ 轴和 $z$ 轴旋转到世界坐标系中的 $R$ 方向。

另外，当我们使用相机拍摄 3D 场景时，会产生透视效果，因此还需要将 3D 点从相机坐标系投影到二维图像平面上，产生 2D 坐标。这个过程可以使用相机内参矩阵 $K$ 进行变换，其变换公式为：

$$P_{image} = KP_{camera}$$

其中 $p_{image}$ 是3D点在二维图像平面上的投影坐标，$K$ 是相机的内参矩阵，包含了相机的焦距、主点和像素尺寸等信息。

在进行透视投影变换时，矩阵 K 包含了相机内参矩阵和图像平面到相机坐标系的映射，从而将相机坐标系中的点投影到了图像平面上。在此过程中，z 轴信息（深度信息）也被保留下来，即图像平面上的点 (u,v) 对应于相机坐标系中的一条射线。如果需要获取该点的三维坐标，则需要进一步的处理，例如使用深度图等信息，将该射线与场景中的物体求交，以获取其三维坐标。

>- 如果想要将相机坐标系下的点投影到图像平面上，通常需要进行归一化，即将 x 和 y 分量除以 z 分量，这样可以将相机坐标系下的点变换为齐次坐标系下的点，并将其投影到图像平面上。
>
>- 在相机坐标系下，$z$ 坐标通常表示相机到物体的距离，也称为深度或距离信息。在计算机视觉中，从图像中提取深度信息是非常重要的任务之一，可以用于三维重建、姿态估计、物体检测等应用。
{: .prompt-tip}



## Visible Surface Discrepancy (VSD) [1,2]:

$e_{VSD}$的特性：由于物体姿态可能是模棱两可的，即可能存在多个无法区分的姿态（这是由于可见部分与整个物体表面之间存在多个拟合，可见部分由自遮挡和其他物体的遮挡确定，多个表面你和由全局或局部物体对称性引起）。$e_{VSD}$ 不考虑颜色信息，且仅在模型表面的可见部分计算，因此`无法区分的位姿被视为等价`。这个版本的定义不惩罚可能由深度传感器或地面真实姿态的不精确性引起的小距离差异。

> $e_{VSD}$ 的不足之处是无法处理对象的全局或部分对称性，这可能导致存在多个可行的位姿，但这也是其他位姿误差函数的一个共同问题。
{: .prompt-warning}

$e_{VSD}$ 的公式如下：

$$e_{VSD}(\hat{S},\bar{S},S_I,\hat{V},\bar{V},\tau) = \frac{1}{|\hat{V} \cup \bar{V}|} \sum_{p\in\hat{V} \cup \bar{V}} \begin{cases} 0 & \text{if } p\in \hat{V} \cap \bar{V} \text{ and } |\hat{S}(p) - \bar{S}(p)| < \tau \\ 1 & \text{otherwise} \end{cases}$$

其中：

- $\hat{S}$ : 通过估计位姿 $\hat{P}$ 渲染出来的深度信息，调用 `misc.depth_im_to_dist_im_fast(depth, K)` 得到的 `distance map` $\hat{S}$
- $\bar{S}$ : 通过真实位姿 $\bar{P}$ 渲染出来的深度信息，调用 `misc.depth_im_to_dist_im_fast(depth, K)` 得到的 `distance map` $\bar{S}$
- $S_I$ : 通过读取当前物体对应的 depth 图像，调用 `misc.depth_im_to_dist_im_fast(depth, K)` 得到的 `distance map` $S_I$
- $\hat{V}$ : 通过比较 $\hat{S}$ 和 $S_I$，调用 `visibility.estimate_visib_mask_gt(dist_test, dist_gt, delta, visib_mode='bop19')` 得到的 `Visibility masks` $\hat{V}$
- $\bar{V}$ : 通过比较 $\bar{S}$ 和 $S_I$，调用 `visibility.estimate_visib_mask_gt(dist_test, dist_est, delta, visib_mode='bop19')` 得到的 `Visibility masks` $\bar{V}$
- $\tau$ : 偏差容忍度（misalignment tolerance）

> distance map 是指在每个像素点 p 处存储该点对应的 3D 点 $x_p$ 到摄像机中心的距离。它可以通过深度图计算得到，深度图在每个像素点 p 处存储对应的 3D 点 $x_p$ 的 Z 坐标值。
{: .prompt-tip}

​    

## Maximum Symmetry-Aware Surface Distance (MSSD)[3]

MSSD (Maximum Symmetry-Aware Surface Distance) 是一种用于评估位姿估计精度的度量方法，它`考虑了物体的全局对称性`。它计算了位于测试图像中可见的物体模型表面点与参考位姿下的模型表面点之间的最大欧几里得距离，其中考虑了全局对称性变换。与 ADD/ADI [2,5] 中使用的平均距离相比，最大距离不太依赖于网格顶点的采样，因为平均距离通常受高频表面部分的支配。

MSSD 的计算公式如下：

$$e_{MSSD}(\hat{P},\bar{P},S_M,V_M)=\min_{S\in S_M}\max_{x\in V_M}||\hat{P}x-\bar{P}Sx||^2$$

其中：

- $\hat{P}$ : 估计位姿 
- $\bar{P}$ : 真实位姿 
- $S_M$ : 全局对称变换的集合（[set of global symmetry transformations](https://bop.felk.cvut.cz/challenges/bop-challenge-2019/#identifyingsymmetries)）
- $V_M$ : 模型 M 的顶点集合

​    

## Maximum Symmetry-Aware Projection Distance (MSPD):

MSPD (Maximum Symmetry-Aware Projection Distance) 是一种评估物体重建结果的方法。它考虑了物体的全局对称性，并通过计算在所有对称变换下的最大距离来增强算法的鲁棒性。该方法使用2D投影操作 (proj) 将三维模型的顶点投影到图像平面上，计算重建结果与真实结果在所有对称变换下的最大距离。MSPD适用于评估 RGB-only 的物体重建方法，并对增强现实应用有重要意义。与2D投影方法相比，MSPD 可以更好地评估模型的对称性和鲁棒性。

MSPD 的计算公式如下：

$$e_{MSPD}(\hat{P},\bar{P},S_M,V_M) = \min_{S \in S_M} \max_{x \in V_M} \left\lVert \operatorname{proj}(\hat{P}x) - \operatorname{proj}(\bar{P}Sx) \right\rVert_2$$

其中：

- $\hat{P}$ : 估计位姿 
- $\bar{P}$ : 真实位姿 
- $S_M$ : 全局对称变换的集合（[set of global symmetry transformations](https://bop.felk.cvut.cz/challenges/bop-challenge-2019/#identifyingsymmetries)）
- $V_M$ : 模型 M 的顶点集合

​    

## 参考

- [1] Hodaň, Michel et al.: [BOP: Benchmark for 6D Object Pose Estimation](http://cmp.felk.cvut.cz/~hodanto2/data/hodan2018bop.pdf), ECCV 2018.

- [2] Hodaň et al.: [On Evaluation of 6D Object Pose Estimation](http://cmp.felk.cvut.cz/~hodanto2/data/hodan2016evaluation.pdf), ECCVW 2016.

- [3] Drost et al.: [Introducing MVTec ITODD - A Dataset for 3D Object Recognition in Industry](http://openaccess.thecvf.com/content_ICCV_2017_workshops/papers/w31/Drost_Introducing_MVTec_ITODD_ICCV_2017_paper.pdf), ICCVW 2017.

​    

## 写在最后

感谢你在茫茫人海中找到我🕵🏼

<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>

<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.3.1/css/all.css" integrity="sha384-mzrmE5qonljUremFsqc01SB46JvROS7bZs3IO2EmfFsd15uHvIt+Y8vEf7N7fWAU" crossorigin="anonymous">

<span id="busuanzi_container_page_pv">🎉你是第 <span id="busuanzi_value_page_pv"><i class="fa fa-spinner fa-spin"></i>  </span> 个读者

㊗️ 你平安喜乐，顺遂无忧！

希望你读完有所收获～

🥂🥂🥂 

