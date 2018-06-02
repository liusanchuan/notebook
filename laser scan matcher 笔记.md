# laser scan matcher 笔记
### 整体流程 
1. `LaserScanMatcher`类中，先`  initParams();`初始化参数，然后设定`ros`的发布节点和接受节点
2. 然后看雷达数据函数`LaserScanMatcher::scanCallback`，其中调用了两个函数
` laserScanToLDP(scan_msg, curr_ldp_scan);`和` processScan(curr_ldp_scan, scan_msg->header.stamp);`
，我们一个一个看  

	- laserScanToLDP函数，把ros的laser_scan转换成本程序定义的雷达格式`struct laser_data`，`typedef struct laser_data* LDP;` 绕了个大弯，这个`LDP` 中包含了里程计信息 `odometry`和真是位置`true_pose` `estimate`

	- processScan函数，把转换过的雷达格式传入
		- 首先预测根据前后两帧的变化时间预测`getPrediction`位姿变化
		- 然后 `createTfFromXYTheta`得到tf转换，**这里发现个好东西`tf::Transform`可以直接运算`pr_ch_l = laser_to_base_ * f2b_.inverse() * pr_ch * f2b_ * base_to_laser_ ;`,他们分别是上一个tf，** ，最后得到当前预测的位置在全局坐标系下的坐标
		- 这里才到了最关键最重要的地方 **ICP**,调用CSM（ [C(anonical) Scan Matcher (CSM)](https://censi.science/software/csm/) ）的点到线的ICP算法`  sm_icp(&input_, &output_)`,看了一晚上了，好像看错了库
		- 后面就是对`sm_icp`求得的结果进行验证，并转换成tf，最后整理发布就完鸟。

3. 关键的关键`sm_icp`

`sm_icp`一个`sm_params`结构体，并返回一个`sm_result`,那么，如果我只想用这个库来完成我的匹配工作，我就只需要知道这个输入的结构，然后调用就完事了。
`sm_params`在[sm_icp手册第7节. Embedding CSM in your programs](https://github.com/AndreaCensi/csm/blob/master/csm_manual.pdf)

接下来就实验一下吧.
`星期三, 30. 五月 2018 07:01下午 `今天试了一下这个库，运行不出来，现在在决定要不要放弃自己写，还是说去改原来的库文件。 
先自己测试下吧，最后实在是不行的话再改原文件吧。
先分析一下当前存在的问题，当前的问题聚焦再`sm_icp`上面：  
	-  一方面可能是输入参数的问题，`params`初始化是不是正确,先检验这个吧
	-  另一方面就是，输入的`sm_result1`的结果的问题
`星期三, 30. 五月 2018 08:42下午 `  
c++编码能力太差了，哎，写个程序，费死个老劲啊

		
	
	
---	
```
tf::Transform 的骚操作：
  input_.first_guess[0] = pr_ch_l.getOrigin().getX(); // 得到xy
  input_.first_guess[1] = pr_ch_l.getOrigin().getY();
  input_.first_guess[2] = tf::getYaw(pr_ch_l.getRotation()); //得到水平旋转角，帅
```
	
	
