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



## 3 代码笔记

### 1.小点

#### 安装

`sudo apt-get install libpcl-dev pcl-tools`

#### 定义一个点云

 `pcl::PointCloud<pcl::PointXYZ>::Ptr cloud(new pcl::PointCloud<pcl::PointXYZ>);`，

#### 从PCD文件中读取点云

 `pcl::PCDReader reader; reader.read("table_scene_lms400.pcd",*cloud);`，

#### 保存点云

- `pcl::io::savePCDFileASCII("test_pcd.pcd",cloud);`，

### 2. 小块

#### 引用 全能王

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

#### 轴向区间滤波

```c++
    pcl::PassThrough<pcl::PointXYZ> pass;
    pass.setInputCloud(cloud);
    pass.setFilterFieldName("z");
    pass.setFilterLimits(0,1);
//    pass.setNegative();
    pass.filter(*cloud_filtered);
```

#### 体素滤波

体素滤波，就是在一个规定大小的立方体内只最多选一个点，其他点全部过滤掉。
不知道为什么在一个电脑上可以运行另一个电脑就运行不了，reader一直报错。先学习吧，后面再看这个问题。

```c++
    pcl::PCLPointCloud2::Ptr cloud(new pcl::PCLPointCloud2); //注意用的点云格式
    pcl::PCLPointCloud2::Ptr cloud_filtered(new pcl::PCLPointCloud2);
    pcl::PCDReader reader;
    reader.read("/home/lt/CLionProjects/pcl_learning/cmake-build-debug/test_pcd.pcd",*cloud);
    cerr<<cloud->width<<" "<<cloud->height<<pcl::getFieldsList(*cloud)<<endl;

    std::cout<<"after"<<std::endl;
    //filter
    pcl::VoxelGrid<pcl::PCLPointCloud2> sor;
    sor.setInputCloud(cloud);
    sor.setLeafSize(0.01f,0.01f,0.01f);
    sor.filter(*cloud_filtered);
    pcl::PCDWriter writer;
    writer.write("table_sense_downsampled.pcd",*cloud_filtered,Eigen::Vector4f::Zero(),Eigen::Quaternionf::Identity(),
                 false);
```

![体素滤波前后的效果](/home/sc/%E6%96%87%E6%A1%A3/%E7%AC%94%E8%AE%B0/img/voxel.png  "体素滤波前后的效果非常不一样") 体素滤波前后的效果，分别设置体素大小为`0.01`和`0.05`

#### 去除外点 滤波

```
    pcl::StatisticalOutlierRemoval<pcl::PointXYZ> sor;
    sor.setInputCloud(cloud);
    sor.setMeanK(50); //每计算一个点根据他附近n个点来决定
    sor.setStddevMulThresh(1.0); // standard deviation multiplier (标准偏差乘子)to 1. 
    sor.filter(*cloud_filtered);
```

### 3 配准 registration

pcl中的配准算法有`ICP` ，`NDT`
[短短几句话，说出了环境重建的核心，好文章](http://pointclouds.org/documentation/tutorials/registration_api.php#registration-api)

**程序示例**

```c++
#include <iostream>
#include <pcl/io/pcd_io.h>
#include <pcl/point_types.h>
#include <pcl/registration/icp.h>
using namespace std;
int  main(int argc,char** argv){
    pcl::PointCloud<pcl::PointXYZ>::Ptr pointCloud1(new pcl::PointCloud<pcl::PointXYZ>);
    pcl::PointCloud<pcl::PointXYZ>::Ptr pointCloud2(new pcl::PointCloud<pcl::PointXYZ>);
    pcl::PCDReader reader;
    reader.read("sample.pcd",*pointCloud1);
    *pointCloud2=*pointCloud1;
    for (int i = 0; i < pointCloud2->points.size(); ++i) {
        pointCloud2->points[i].x+=0.3;
        pointCloud2->points[i].y-=0.2;
    }
    pcl::IterativeClosestPoint<pcl::PointXYZ,pcl::PointXYZ> icp;
    icp.setInputSource(pointCloud1);
    icp.setInputTarget(pointCloud2);
    pcl::PointCloud<pcl::PointXYZ> final;
    icp.align(final);
    std::cout << "has converged:" << icp.hasConverged() << " score: " <<
              icp.getFitnessScore() << std::endl;
    std::cout<<icp.getFitnessScore()<<" "<<endl;
    std::cout<<icp.getFinalTransformation()<<" "<<endl;
    return 0;
}
```

需要注意的几点：
ICP中电云的定义必须要是`    pcl::PointCloud<pcl::PointXYZ>::Ptr pointCloud1(new pcl::PointCloud<pcl::PointXYZ>);`这样子的，指针型





