# ROS的launch文件常用语法
[TOC]  
### 1. 最外围
必须有 ` <launch> ... ... </launch>` 
### 2. 节点
<br/>
```
<node 
	pkg="包名" 
	type="节点名" 
	name="对节点名重命名，覆盖ros::init中的名字（一般直接写成type同名）"
	args="节点传入参数，会直接赋值给ros::init的argv" 
	ns="把当前节点归到一个命名空间下">
</node>
```  
- 节点重命名  
```
<remap from="original-name" to="new-name" />  
```

### 3.  including引入其他launch文件  
用一个find文件找到其他包的主目录，就是cmakelist文件的目录

	<include file="$(find package-name)/.../launch-file-name" />

### 4. 参数param
 - 引入参数文件

```
<rosparam command="load" file="$(find rosparam)/example.yaml" />  ```  
-  直接设定参数  
`<arg name="whitelist" default="[3, 2]"/> `  
- param设定参数
`<param name="foo" value="$(arg my_foo)" />`
> 上面的arg 变量必须对应一个`<arg ... />` 标签，arg中的参数包括 ` name="arg名字"`，`default="默认值"`，`value=“arg值”`
 
### 5. 发布tf静态坐标转换 （欧拉角和四元数）
- 欧拉角表示  
`  <node pkg="tf" 
	type="static_transform_publisher" 
	name="base_link_to_laser"
    	args="0.0 0.0 0.0 0.0 0.0 0.0 /base_link /laser 40" />`
    其中 `args="0.0 0.0 0.0 0.0 0.0 0.0 /base_link /laser 40"`中，前三个数是xyz坐标，后三个是`yaw` `pitch` `roll` 三个角度`yaw`是围绕x轴旋转的偏航角，`pitch`是围绕y轴旋转的俯仰角，`roll`是围绕z轴旋转的翻滚角），在后面是两个frame id，第一个是`/base_link`是父坐标系，后一个是子坐标系，最后一个参数`40`是period_in_ms 即40ms发送一次。
- 四元数表示
` static_transform_publisher`的参数顺序` x y z qx qy qz qw frame_id child_frame_id  period_in_ms` 
当表示二维坐标时有一个简单的方法，绕z轴转`a°`，则`[qx qy qz qw] = [0  0  sin(a)  cos(a)]`,哈哈，非常简单，仅适用于二维机器人。这是一个[四元数可视化网站](https://quaternions.online/) 。
 


	
--- 


### 参考文献
	https://www.jianshu.com/p/0130ad32a08d 
	http://wiki.ros.org/roslaunch ros中launch文件的格式

