# Eigen 笔记
[TOC]

### 1. 引入

`cmake` 中不用管
头文件添加
~~~ c
#include <Eigen/Core>
#include <Eigen/Geometry> //几何模块
~~~

### 2. 常用类型

- `Eigen::Isometry3d`表示欧式变换矩阵，括**旋转**，**转换**
- ` Eigen::Quaterniond q(qw,qx,qy,qz );`四元数向量
- `Eigen::aligned_allocator<Eigen::Isometry3d>`内存对齐