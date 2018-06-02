# PCL学习笔记
[TOC]

`PCL`教程学习包括以下几部分

- Basic Usage
- Advanced Usage
- Applications
- Features
- Filtering
- I/O
- Keypoints `关键点`
- KdTree `最近邻搜索算法` 
- Octree `八叉树`
- Range 
- Recognition `识别`
- Registration `配准` 把连续的几帧点云组合成一个一致性的点云
- Sample Consensus `RANSAC ` 
- Segmentation `分割`
- Surface `点云转曲面`
- Visualization `可视化`
- GPU

## 1. 先学习第一部分介绍吧
[教程链接](http://pointclouds.org/documentation/tutorials/)
`PCL`包括 

- 滤波:` 去除外点`

- 特征、关键点、匹配、分割等模块

  

## 2  [基础数据类型](http://pointclouds.org/documentation/tutorials/basic_structures.php#basic-structures)

PCL中最基本的数据类型就是`PointCloud`，它包括`width`, `height`,`points(std::vector<PointT>) ` `PointT` 是最基本的点的类型,

`is_dense`,`sensor_origin_`Sensor acquisition pose (origin/translation),`sensor_orientation`Sensor acquisition pose (rotation) 



引用全能王

```shell
cmake_minimum_required(VERSION 2.8 FATAL_ERROR)
project(passthrough)
find_package(PCL 1.2 REQUIRED)

include_directories(${PCL_INCLUDE_DIRS})
link_directories(${PCL_LIBRARY_DIRS}) #所有的头文件
add_definitions(${PCL_DEFINITIONS})

add_executable (passthrough passthrough.cpp)
target_link_libraries (passthrough ${PCL_LIBRARIES}) #所有的库
```

