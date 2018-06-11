# Gtsam学习笔记

[TOC]

### cmake引入

```shell
# 寻找第三方库，使用大小写都可以，这里列举了两种方式
find_package(Boost COMPONENTS thread filesystem date_time system REQUIRED)
FIND_PACKAGE(GTSAM REQUIRED)

# 包含第三方库头文件路径，可以使用绝对路径
INCLUDE_DIRECTORIES(${Boost_INCLUDE_DIR})
INCLUDE_DIRECTORIES(${GTSAM_INCLUDE_DIR})
INCLUDE_DIRECTORIES("/usr/include/eigen3")

add_executable(gtsam_test main.cpp)
# 链接库 
target_link_libraries(gtsam_test ${Boost_LIBRARIES} -lgtsam -ltbb)
install(TARGETS gtsam_test RUNTIME DESTINATION bin)
```

`clion`没法识别的话就重启一下，可能是有的玩意没刷新出来。

### 因子factor

#### 预定义的factor

```c++
头文件
#include <gtsam/slam/BetweenFactor.h> //二元因子，位姿之间，回环之间
#include <gtsam/slam/PriorFactor.h> //一元因子，系统先验
//定义因子图
NonlinearFactorGraph graph;
//噪声定义 对角线矩阵：
noiseModel::Diagonal::shared_ptr model = noiseModel::Diagonal::Sigmas(Vector3(0.2, 0.2, 0.1));
//在因子图中加入一个因子
//   二元因子
graph.emplace_shared<BetweenFactor<Pose2> >(1, 2, Pose2(2, 0, 0     ), model);
			//参数解释 ^ ：因子类型  ^边		key 1、2  ^ 边的值			^ 噪声模型
//   一元因子
graph.emplace_shared<PriorFactor<Pose2> >(1, Pose2(0, 0, 0), priorNoise);
```

#### 生成factor

```c++
  Rot2 prior = Rot2::fromAngle(30 * degree);
  prior.print("goal angle"); 
  noiseModel::Isotropic::shared_ptr model = noiseModel::Isotropic::Sigma(1, 1 * degree);
  Symbol key('x',1); // 一个key就是一个label
  PriorFactor<Rot2> factor(key, prior, model);
```

### 初值定义

一个图中要给每一个变量赋予一个初始值。

```c++
  Values initialEstimate; //定义初始值容器
  initialEstimate.insert(1, Pose2(0.5, 0.0,  0.2   )); //加入一个变量 arg1：变量的标签 arg2:这个变量的值
```

### 优化方法

#### GaussNewton法

引入 `#include <gtsam/nonlinear/GaussNewtonOptimizer.h>`

```c++
GaussNewtonParams parameters;//参数对象
parameters.relativeErrorTol = 1e-5; // 迭代变化量小于这个值就退出
parameters.maxIterations = 100; //最大迭代次数
//创建一个高斯牛顿优化器 arg：		因子图 初值				参数对象
GaussNewtonOptimizer optimizer(graph, initialEstimate, parameters);
//求出优化解 得到变量的最优值
Values result = optimizer.optimize();
```

#### LevenbergMarquardt法

引入 `#include <gtsam/nonlinear/LevenbergMarquardtOptimizer.h>`

```
LevenbergMarquardtOptimizer optimizer(graph,initialEstimate);
Values result=optimizer.optimize(); 
```

### 边缘化 marginal

```c++
    Marginals marginals(graph,result);
    for (int j = 1; j < 4; ++j) {
        boost::format fmt("\n x%1% covariance: \n %2% \n");
        fmt%j%marginals.marginalCovariance(j); //得到每个变量的协方差
        cout<<fmt;
    }
```

### 读写 g2o文件

```c++
#include <gtsam/slam/dataset.h> //引入头文件
//读
NonlinearFactorGraph::shared_ptr graph1;
Values::shared_ptr initialEstamate1;
string g2ofile=findExampleDataFile("noisyToyGraph.txt");
bool is3D =false;
boost::tie(graph1,initialEstamate1)=readG2o(g2ofile,is3D);
//写 g2o文件
writeG2o(graph,result,"graph2g2o.g2o"); //args: 因子图 ，变量结果，目标文件名
```

