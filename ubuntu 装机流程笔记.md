#ubuntu 双系统装机流程笔记-三川

`星期三, 06. 六月 2018 04:59下午 `

[TOC]
## 装ububtu 
### 准备好启动盘
官网下载，U盘制作。
### 压缩出空余硬盘
在window系统中的磁盘管理项，找一个空闲大的磁盘，压缩出100G左右的空闲存储空间。关机。
### BIOS 修改
插上启动盘，`f2`进入BIOS，更改`boot` 顺序，把启动盘的名字放在第一个。然后`F10`保存退出
### 正式安装
重启后进入`Ubuntu试用`，点击桌面上的`install`，选择安装方式为	`其他`不要选`与Windows共存`，不然没法改自定义的分区。
### 分区
然后是分区。分四个区 :

- 系统分区 `/`, 类型:逻辑分区，一般为总空间的20~30%，
- 交换分区 `/sweap`，类型 无， 一般为4G；
- 用户分区 `/home` ,类型:逻辑分区, 一般为剩下的全部，越大越好，60~70%；
- 引导分区`/boot`,类型为主分区,>400M，**这个最后再分**；

还有一个重要的一步，**选择引导项**为`/boot 分区的硬盘号`

### 等待安装
...  waiting
一般不要选择安装中更新，装不装的好还不一定呢，装好了改了**源**以后再更新才是妥妥的。
... 
### 更新 引导`grub` 选项
在终端中，输入  
`sudo update-grub`  
`sudo grub-install /dev/sda`  
这是一个师弟给我说的，这样才能让BIOS引导到新装的系统上。
### 更新源
在`系统设置`-`软件与更新`-`下载自`-`其他站点`-`选择最佳服务器`；

不要用网上给的，更改**源**的文件，这样会有一些bug，新Ubuntu已经糅合了国内很多源了。

### 安装`aptitude`
用`aptitude`代替`apt-get`,听说可以很好的解决版本的依赖问题。
两者的用法一模一样。

## 安装`ROS`
机器人重度玩家，不装`ros`要Ubuntu干嘛？

- 添加源：`sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'`  
- 设置keys：` sudo apt-key adv --keyserver hkp://ha.pool.sks-keyservers.net:80 --recv-key 0xB01FA116`
- 更新安装：  
` sudo apt-get update`  
` sudo apt-get install ros-kinetic-desktop-full`  
- 初始化
`sudo rosdep init`  
`rosdep update`

- 环境配置
`echo "source /opt/ros/kinetic/setup.bash" >> ~/.bashrc` 让bash能找到ROS  
` source ~/.bashrc` 刷新一下
- 测试下小乌龟
`roscore`  
`rosrun turtlesim turtlesim_node `
`rosrun turtlesim turtle_teleop_key `
## 装常用软件  

本人常用的软件有 `vs code`,`clion`,`有道词典`，`搜狗拼音`，具体在另一篇文章里

---
all well done   
 开始全新的学习之旅吧.......




