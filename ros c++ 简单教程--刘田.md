# ros c++ 简单教程--刘田

[TOC]

### cmake怎么写?

• `ADD_EXECUTABLE(bin_file_name) ${SRC_LIST})`  添加一个可执行二进制文件
	○ arg1:可执行文件名 arg2: 所包含的c++文件
• `TARGET_LINK_LIBRARIES(target lib1 lib2 …)`  为target添加需要链接的共享库
	○ arg1:上一个中的可执行文件名;arg2+:共享库
• `set(ProjectName, "myproj")`

. `project(${ProjectName})`    定义工程文件



### catkin怎么用?

#### • 新建一个ros_package

`mkdir -p ~/catkin_ws/src`
`catkin_create_pkg beginner_tutorials std_msgs rospy roscpp std_msgs`

arg1: ros package 名字; arg2+: 所需要添加的ros库的名字 运行后会生成一下目录结构文件

```
package_1/
 CMakeLists.txt     -- CMakeLists.txt file for package_1
 package.xml        -- Package manifest(清单) for package_1
```

#### • cmake中catkin怎么写?

```
find_package(catkin REQUIRED COMPONENTS roscpp rospy std_msgs)  引入catkin 库
target_link_libraries(beginner_tutorials_node ${catkin_LIBRARIES} ) 为可执行文件添加catkin库
```


但是,自己在camke中添加的catkin包,还需要加上,package.xml文件，这个文件，需要可以从别的地方拷贝过来，但是要改 <name>你的工程名</name>
• catkin常引用的库有哪些？
catkin常引用的库有，ros消息库，ros功能包等。
ros内置的msgs有 actionlib_msgs | diagnostic_msgs | geometry_msgs | nav_msgs | sensor_msgs | shape_msgs | stereo_msgs | trajectory_msgs | visualization_msgs，具体的名字可以参考这个链接：『http://wiki.ros.org/common_msgs 』，需要哪个就直接写入到『findpackage（catkin ）』里面 

### c++中ros 常用语法

#### • c++中ros头文件怎么写？

- a.`#include "ros/ros.h" // ros库`

`#include "std_msgs/String.h" // 消息库，`

#### C++ 基本节点订阅发布的关键函数？

- b. `ros::init(argc, argv, "talker");` arg1,2：需要接收主程序的输入参数来remap节点；arg3：node节点名

  其中每一个可执行文件中只能包含一个 init：

- c. `ros::NodeHandle n; //nodehandle` 是c++与ros系统交互的主要入口，第一个初始化后，再新建一个会销毁掉之前定义的nodehandle

- d.` ros::Publisher chatter_pub = n.advertise<std_msgs::String>("chatter", 1000);` //发布节点 arg1: "topic  name "; arg2: 是发布序列的大小 （单位是个）。如果我们发布的消息的频率太高，缓冲区中的消息在大于 1000 个的时候就会开始丢弃先前发布的消息。

- e.  `chatter_pub.publish(msg);`  把msg数据发布出去

- f. `ros::Rate loop_rate(10);` 定义自循环的频率，它会追踪记录自上一次调用 Rate::sleep() 后时间的流逝，并休眠直到一个频率周期（0.1秒）的时间。

- g. `loop_rate.sleep();` 休眠一段时间以使得发布频率为 10Hz

- h. `while (ros::ok())` 

  如果下列条件之一发生，`ros::ok()` 返回false：

  - `SIGINT` 被触发 `(Ctrl-C)`
  - 被另一同名节点踢出 ROS 网络
  - `ros::shutdown()` 被程序的另一部分调用
  - 节点中的所有 ros::NodeHandles 都已经被销毁

- i. `ROS_INFO` 和其他类似的函数可以用来代替 `printf/cout` 等函数

- j. `ros::Subscriber sub = n.subscribe("chatter", 1000, chatterCallback);` 订阅节点 arg1：所订阅的节点名字；arg2：指定最大缓存多少条数据；arg3：回调函数，接收到节点topic msgs就调用一次

- k. `void chatterCallback(const std_msgs::String::ConstPtr& msg)` // 消息以 boost shared_ptr指针形式进行存储，所以不需要进行复制

- l. `ros::spin();` 自循环调用，直到`ros::ok()` 返回`false`，就跳出自循环。`ros::spin`会尽量快的调用回掉函数

### • 发布函数和接受函数的简单示例

#### • 发布函数

```c++
#include "ros/ros.h"
#include "std_msgs/String.h"
#include <sstream>
int main(int argc, char **argv)
{
  ros::init(argc, argv, "talker");
  ros::NodeHandle n;
  ros::Publisher chatter_pub = n.advertise<std_msgs::String>("chatter", 1000);
  ros::Rate loop_rate(10);
  int count = 0;
  while (ros::ok())
  {
 std_msgs::String msg;
 std::stringstream ss;
 ss << "hello world " << count;
 msg.data = ss.str();
 ROS_INFO("%s", msg.data.c_str());
 chatter_pub.publish(msg);
 ros::spinOnce();
 loop_rate.sleep();
 ++count;
  }
  return 0;
}
```



#### • 接受函数

```c
#include "ros/ros.h"
#include "std_msgs/String.h"
void chatterCallback(const std_msgs::String::ConstPtr& msg)
{
  ROS_INFO("I heard: [%s]", msg->data.c_str());
}
int main(int argc, char **argv)
{
  ros::init(argc, argv, "listener");
  ros::NodeHandle n;
  ros::Subscriber sub = n.subscribe("chatter", 1000, chatterCallback);
  ros::spin();
  return 0;
}
```



#### • 类的方法实现订阅发布

```c
#include "ros/ros.h"
#include "std_msgs/String.h"
#include "sensor_msgs/LaserScan.h"
#define THRESH 0.7
class LaserProcess{
private:
 ros::NodeHandle nh_;
 ros::Subscriber laser_sub;
 ros::Publisher laser_pub;
public:
 LaserProcess(){
laser_sub=nh_.subscribe<sensor_msgs::LaserScan>("scan",1000,&LaserProcess::callback,this);
laser_pub=nh_.advertise<sensor_msgs::LaserScan>("new_scan",1000);
ros::spin();
 }
 void callback(sensor_msgs::LaserScan scan){
for(int i=0;i< scan.ranges.size();i++){
    if(scan.ranges[i]>THRESH){
        scan.ranges[i]=0;
        std::cout<<scan.ranges[i];
        laser_pub.publish(scan);
        ROS_INFO("%s",scan.ranges[i]);
    }
}
 }
};
int main(int argc,char ** argv){
 ros::init(argc,argv,"laser_fix");
 LaserProcess las;
}
```

### ros 中的服务怎么用？

#### 三种基本服务类型是什么？

- [Empty](http://docs.ros.org/api/std_srvs/html/srv/Empty.html)
- [SetBool](http://docs.ros.org/api/std_srvs/html/srv/SetBool.html) 
- [Trigger](http://docs.ros.org/api/std_srvs/html/srv/Trigger.html)

#### 服务的服务程序怎么写？

```c++
#include <ros/ros.h>
#include <std_srvs/SetBool.h>
bool flag=false;
// 返回值 bool表示服务器相应是否正常，  arg1：为输入参数，arg2为输出参数
bool callback(std_srvs::SetBoolRequest&req,std_srvs::SetBoolResponse &response){
    flag=req.data;
    if(req.data==true){
        response.success=true;
        response.message="well done";
        ROS_INFO("well done");
    }else{
        response.success= false;
        response.message="not very good";
        ROS_INFO("not very good");
        return 0;
    }
    return 1;
}
int main(int argc,char** argv){
    ros::init(argc,argv,"srv_test");
    ros::NodeHandle n;
    // arg1 服务名，arg2 回调函数
    ros::ServiceServer serviceServer=n.advertiseService("trigger_on",callback);
    ros::spin();
}
```

#### 服务的调用程序怎么写？

```c++
#include <ros/ros.h>
#include <std_srvs/SetBool.h>
int main(int argc,char** argv){
    ros::init(argc,argv,"srv_call");
    ros::NodeHandle n;
    // arg1 服务名，arg2 回调函数
    ros::ServiceClient serviceClient=n.serviceClient<std_srvs::SetBool>("trigger_on");
    std_srvs::SetBool request;
    request.request.data= false;
    if(serviceClient.call(request)){
        std::cout<<request.response.message;
        std::cout<<"--- ";
    }
    return 0;
}
```

