# PCL 代码 笔记

[TOC]

### 1.小点

#### 安装

`sudo apt-get install libpcl-dev pcl-tools`

#### 定义一个点云

 `pcl::PointCloud<pcl::PointXYZ>::Ptr cloud(new pcl::PointCloud<pcl::PointXYZ>);`，

#### 从PCD文件中读取点云

 `pcl::PCDReader reader; reader.read("table_scene_lms400.pcd",*cloud);`，

#### 保存点云

-  `pcl::io::savePCDFileASCII("test_pcd.pcd",cloud);`，

### 2. 小块

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

```c++
    pcl::PCLPointCloud2::Ptr cloud(new pcl::PCLPointCloud2);
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





