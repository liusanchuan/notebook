# Turtlebot3 改装四轮驱动

[TOC]

> 改装思路，将原来后驱的两轮的左右速度分别复制给新添加的同侧电机，然后修改ROS 的tf树，使旋转中心在新添加的旋转中心上。
>
> 具体改装过程分为四个步骤：

### 1. 硬件连接

在`opencr`上添加两个`dynamixal`电机，并设计电机的ID为3和4;

去掉原来waffle上面的万向轮，把新添加的两个电机固定上

### 2. Arduino 程序修改

*Waffle华夫型*:`点击File → Examples → turtlebot3 → turtlebot_waffle → turtlebot3_core`

#### init 中添加

```c
left_wheel_idDown_=3;
right_wheel_idDown_=4;
setTorque(left_wheel_idDown_, true); //使能新添加的电机
setTorque(right_wheel_idDown_, true);
```

如果需要修改`dynamixal`的ID:

 - 需要先获得当前ID，连接这个电机和opencr，使用arduinoIDE中的`opeccr--DynamixelWorkbench--Find Dynamixel`例子，会循环遍历每一个ID，并返回其结果当前链接的。

 - 修改ID，使用arduinoIDE中的`opeccr--DynamixelWorkbench--d_ID_change`例子，修改

   ```c
   #define DXL_ID      1  //当前ID
   #define NEW_DXL_ID  2 	//新ID
   ```

#### speedControl 修改

在`turtlebot3_motor_driver.cpp`文件中，修改`bool Turtlebot3MotorDriver::speedControl`函数。

修改后的函数：

```c++
bool Turtlebot3MotorDriver::speedControl(int64_t left_wheel_value, int64_t right_wheel_value)
{
  bool dxl_addparam_result_;
  int8_t dxl_comm_result_;
  dxl_addparam_result_ = groupSyncWriteVelocity_->addParam(left_wheel_id_, (uint8_t*)&left_wheel_value);
  if (dxl_addparam_result_ != true)
    return false;
  // 分别把传入的左右轮控制参数赋值给同侧新添加的电机
  int64_t right_wheel_valueDown=right_wheel_value; //新添加
  int64_t left_wheel_valueDown=left_wheel_value; //新添加
  dxl_addparam_result_ = groupSyncWriteVelocity_->addParam(right_wheel_id_, (uint8_t*)&right_wheel_valueDown);
  if (dxl_addparam_result_ != true)
    return false;
  //把新添加的两个轮的参数加入到同步电机控制组中
  dxl_addparam_result_ = groupSyncWriteVelocity_->addParam(left_wheel_idDown_, (uint8_t*)&left_wheel_valueDown);  //新添加
  dxl_addparam_result_ = groupSyncWriteVelocity_->addParam(right_wheel_idDown_, (uint8_t*)&right_wheel_value);  //新添加
  dxl_comm_result_ = groupSyncWriteVelocity_->txPacket();
  if (dxl_comm_result_ != COMM_SUCCESS)
  {
    packetHandler_->printTxRxResult(dxl_comm_result_);
    return false;
  }
  groupSyncWriteVelocity_->clearParam();
  return true;
}
```

### 3. tf 更新

原先的旋转中心在后轮的两轮中点，现在新添加了四个轮后，就到了四个轮组成的矩形的几何中心了。

![原始链接](/home/lt/%E6%96%87%E6%A1%A3/%E7%AC%94%E8%AE%B0/data/tb2link_origin.png)

我们可以看到当前的，旋转中心在，后轮的中间，所以我们需要把baselink发布到改装后的四轮中心。需要根据对应的安装位置更改对应的`tf tree`



