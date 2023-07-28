---
layout: post
title: Mac 使用 Home-brew 安装 PCL 和 OPENCV，并在 CLion 中使用
date: 2023-07-25 18:32 +0800
categories:
- 学习随笔
- C++
tags:
- C++
- CLion
- CMakeLists
---



## 安装 PCL 和 OPENCV

```bash
brew install pcl
brew install opencv
brew install cloudcompare
```



## CLion 中的 CMakeLists.txt 编写

```cmake
cmake_minimum_required(VERSION 3.21)
project(GlueSeg)  # 项目名称

set(CMAKE_CXX_STANDARD 14)

# Opencv 配置
find_package(OpenCV REQUIRED)
include_directories(${OpenCV_INCLUDE_DIRS})

# PCL 配置
find_package(PCL REQUIRED)
include_directories(${PCL_INCLUDE_DIRS})
link_directories(${PCL_LIBRARY_DIRS})
add_definitions(${PCL_DEFINITIONS})

add_executable(GlueSeg main.cpp)
# 项目添加 Opencv 和 PCL 的链接
target_link_libraries(GlueSeg ${OpenCV_LIBS} ${PCL_LIBRARIES})

```



## 生成点云数据

```c++

#include <iostream>
#include <pcl/io/pcd_io.h>
#include <pcl/point_types.h>

int main ()
{
    pcl::PointCloud<pcl::PointXYZ> cloud;

    // Fill in the cloud data
    cloud.width    = 200;
    cloud.height   = 200;
    cloud.is_dense = false;
    cloud.resize (cloud.width * cloud.height);

    for (auto& point: cloud)
    {
        point.x = 1024 * rand () / (RAND_MAX + 1.0f);
        point.y = 1024 * rand () / (RAND_MAX + 1.0f);
        point.z = 1024 * rand () / (RAND_MAX + 1.0f);
    }

    pcl::io::savePCDFileASCII ("test_pcd.pcd", cloud);
    std::cerr << "Saved " << cloud.size () << " data points to test_pcd.pcd." << std::endl;

    for (const auto& point: cloud)
        std::cerr << "    " << point.x << " " << point.y << " " << point.z << std::endl;

    return (0);
}

```

> 生成的 pcd 文件可以使用 `cloudcompare 软件`打开



## 参考

- [macOS + PCL + CLion + CloudCompare 生成点云数据](https://blog.csdn.net/qq_33177268/article/details/124497876)



## 写在最后

感谢你在茫茫人海中找到我🕵🏼

<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>

<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.3.1/css/all.css" integrity="sha384-mzrmE5qonljUremFsqc01SB46JvROS7bZs3IO2EmfFsd15uHvIt+Y8vEf7N7fWAU" crossorigin="anonymous">

<span id="busuanzi_container_page_pv">🎉你是第 <span id="busuanzi_value_page_pv"><i class="fa fa-spinner fa-spin"></i>  </span> 个读者

㊗️ 你平安喜乐，顺遂无忧！

希望你读完有所收获～

🥂🥂🥂 
