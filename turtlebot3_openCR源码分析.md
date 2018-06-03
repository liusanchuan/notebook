# turtlebot3 OpenCR源码分析

首先，打开Arduino IDE中文件`File → Examples → turtlebot3 → turtlebot_waffle → turtlebot3_core`

包括四个文件：

```sh
turtlebot3_core.ino		-- Arduino主文件     
turtlebot3_motor_driver.h		-- 电机控制头文件
turtlebot3_motor_driver.cpp		-- 电机控制文件
turtlebot3_core_config.h  		--配置文件
```

### 1 turtlebot3_core

在主文件目录中，首先设置订阅和发布的ROS节点话题。

> 订阅：速度指令`cmd_vel` ，
>
> 发布：传感器状态 `sensor_state`，陀螺仪里程计`imu`，编码器里程计`odom`，链接状态`joint_states`
>
> TF状态：tfbroadcaster

然后在`void setup()`函数中初始化各个节点话题。

接着在`void loop()`中，主要包括`receiveRemoteControlData()`，`controlMotorSpeed()`，然后发布`速度`，`publishSensorStateMsg();`，`publishDriveInformation()`，`publishImuMsg()`等传感器的信息。最后检查自身的按钮状态，并更新`IMU`。

- 先调用`receiveRemoteControlData()`函数接收上位机发布的控制数据。
- 然后把接收到的速度在`controlMotorSpeed`函数中发布给`DYNAMIXEL`电机，这部分着重分析。
  - 在`controlMotorSpeed`中首先把当前速度和目标速度按分辨率离散开逐步改变到目标速度`wheel_speed_cmd[LEFT]  = goal_linear_velocity - (goal_angular_velocity * WHEEL_SEPARATION / 2);`，
  - 然后把生成的左右轮速度发送给` motor_driver.speedControl((int64_t)lin_vel1, (int64_t)lin_vel2);` ，这个函数中会把实际的速度发送给电机。
    - 在`bool Turtlebot3MotorDriver::speedControl(int64_t left_wheel_value, int64_t right_wheel_value)`函数中，主要是把每一个电机的ID和速度加入到电机组同步控制`dynamixel::GroupSyncWrite`的参数中` groupSyncWriteVelocity_->addParam(left_wheel_id_, (uint8_t*)&left_wheel_value)`

### 2 Turtlebot3MotorDriver类

`Turtlebot3MotorDriver`类的主要构造如下：

```c++
class Turtlebot3MotorDriver
{
 public:
  Turtlebot3MotorDriver();
  ~Turtlebot3MotorDriver();
  bool init(void);	//初始化dynamixel的port和packet，使能电机ID，初始化电机组同步读写类
  void closeDynamixel(void);
  bool setTorque(uint8_t id, bool onoff); //设置使能电机ID
  bool readEncoder(int32_t &left_value, int32_t &right_value);
  bool speedControl(int64_t left_wheel_value, int64_t right_wheel_value); //速度控制
 private:
  uint32_t baudrate_;
  float  protocol_version_;
  dynamixel::PortHandler *portHandler_;
  dynamixel::PacketHandler *packetHandler_;

  dynamixel::GroupSyncWrite *groupSyncWriteVelocity_; //组同步读
  dynamixel::GroupSyncRead *groupSyncReadEncoder_;	//组同步写
};
```

在这个类中，需要关注的就是对电机的增删修改操作，主要步骤是，先在init中添加对应的电机ID，然后

添加使能，最后把对定的控制变量添加到`speedcontrol`对应的函数中。