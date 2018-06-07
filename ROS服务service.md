# ros servs

### 三种消息：

- [Empty](http://docs.ros.org/api/std_srvs/html/srv/Empty.html)
- [SetBool](http://docs.ros.org/api/std_srvs/html/srv/SetBool.html) 
- [Trigger](http://docs.ros.org/api/std_srvs/html/srv/Trigger.html)

### 服务的服务程序：

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

### 服务的调用程序

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

