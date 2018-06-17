# Orb_slam2 笔记

[TOC]

### 安装

安装总目录里面有

### 数据集准备

- [TUM](https://vision.in.tum.de/data/datasets/rgbd-dataset/download) 先测试[fri/room](http://filecremers3.informatik.tu-muenchen.de/rgbd/dataset/freiburg1/rgbd_dataset_freiburg1_room.tgz) 下载好了以后存盘 

- 然后用 网站提供的 [关联工具](https://svncvpr.in.tum.de/cvpr-ros-pkg/trunk/rgbd_benchmark/rgbd_benchmark_tools/src/rgbd_benchmark_tools/associate.py)  这个工具要这样用`python associate.py rgb.txt depth.txt > associate.txt` 来自[高翔博客](https://www.cnblogs.com/gaoxiang12/p/5175118.html) 

  ```shell
   Examples/RGB-D/rgbd_tum Vocabulary/ORBvoc.txt Examples/RGB-D/TUM1.yaml ~/下载/rgbd_dataset_freiburg1_room ~/下载/rgbd_dataset_freiburg1_room/associate.txt 
   #可执行文件 arg：词袋文件 相机标定文件 数据集文件夹 数据集关联文件
  ```

  

### 在项目中使用 Orb_slam2s

参考[博客](https://blog.csdn.net/aptx704610875/article/details/51490201) [源码中Example](https://github.com/raulmur/ORB_SLAM2/tree/master/Examples) 

