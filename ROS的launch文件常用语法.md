# ROS的launch文件常用语法
`星期二, 29. 五月 2018 07:45下午 `


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
- 1.1.节点重命名  
<br/>
```
<remap from="original-name" to="new-name" />  
```
- 1.2. 
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
 


	
--- 


### 参考文献
	https://www.jianshu.com/p/0130ad32a08d 
	http://wiki.ros.org/roslaunch ros中launch文件的格式

