# turtlebot 售后问题总结

更新时间: 2018年06月17日15:47:20

[TOC]

###  OpenCR

#### openCr 固件烧不进去怎么办?

如果固件没烧写好，按住`bt2`然后按`reset`，再松开两个,这时进入重置模式，再重新烧写固件

#### openCr 程序烧不进去怎么办?

如果程序烧不进去,仍然是按住`bt2`然后按`reset`,再松开,然后烧录程序就可以烧好了

#### OpenCR开机一直响!?

首先是电池没电了,opencr电源模块检测到低电压时,发出警报,这时,充电就行了.

#### ros控控制turtlebot,转向和命令不一致怎么办?

- 先检查两个电机的顺序有没有装反,面向前进方向,左边是1号电机(有标签),右边是2号.
- 单独观察左右两轮,按前后左右方向,看是哪个轮的转向错误导致的.如果只是其中一个轮与正常控制反向相反,就更改 `OpenCR`的`arduino` 相应的速度控制变量反向,就是加一个负号

### Turtlebot 电源

#### turtlebot 能否边给电池充电边使用?

不能

#### 树莓派和intel Joule装系统时,怎么供电

不要用**电池**供电,用**电源**直接供电

### 树莓派

#### 树莓派插上显示器开机什么也没有怎么办?

首先查看 SD卡有没有插上,再看SD卡里面有没有烧系统.

#### RealSense 无法正常使用怎么办?

分两方面来检查,`R200相机`和`intelJoule驱动`

- 先找一个装有Ubuntu系统的电脑测试：安装 `sudo apt-get install ros-kinetic-librealsense `，`sudo apt-get install ros-kinetic-realsense-camera `，然后运行`roslaunch realsense_camera r200_nodelet_default.launch `，再用`rqtimage_view`查看,更改对应的图像节点名字,如果能够显示出来,就不是R200相机的问题.否则就是相机坏了.
- 再检查Intel Joule的驱动有没有装好,可以重新装一遍,注意观察有没有报错提示,如果有并且是系统内核不对,高端玩家可以试着改内核,新手就重装系统再来一遍吧.

### R200驱动怎么装?

- 安装R200驱动 参考[官网](http://emanual.robotis.com/docs/en/platform/turtlebot3/appendix_realsense/#installation) 以及 [ROS wiki](http://wiki.ros.org/librealsense) 

```shell
$ sudo apt-get install linux-headers-generic
$ sudo apt-get install ros-kinetic-librealsense

#稳定版
$ cd catkin_ws/src
$ git clone https://github.com/intel-ros/realsense.git
$ cd realsense
$ git checkout 1.8.0
$ cd catkin_ws && catkin_make -j2
```

