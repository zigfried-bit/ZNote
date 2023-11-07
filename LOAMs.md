<!--
 * @Author: zigfried 3572931733@qq.com
 * @Date: 2023-10-25 21:30:51
 * @LastEditors: zigfried 3572931733@qq.com
 * @LastEditTime: 2023-10-25 21:51:47
 * @FilePath: /A-LOAM/home/zigfried/文档/ZNote/LOAMs.md
 * @Description: 
 * 
 * Copyright (c) 2023 by zigfried, All Rights Reserved. 
-->
## LOAMs
`by zig 2023.10.25` 
简要总结一下LOAM系列算法
首先，LOAM这个词取自Lidar Odometry and Mapping
### ALOAM
LOAM的代码优化版本，采用ceres库做非线性优化，而非原来的手撕LMA；再就是完全取消了IMU。
太久没看，有些细节忘记了，借此回忆一下

从一个问题出发：有种说法叫做“LOAM是一个激光里程计，只有前端没有后端”，这句话该怎么理解呢？

ALOAM算法就3个核心文件：
- `scanRegistration.cpp`
- `laserOdometry.cpp`
- `laserMapping.cpp`

特征提取这块儿看得比较多了，就是分别提取edge point和planar point

里程计这块逻辑是：
1. 接收特征点，时间同步（处理回调）
2. 点云配准（构造ceres ResidualBlock），求解2次

建图模块看得比较少，建图是单独开一个线程
单独开一个线程的逻辑是：建图线程其实就有一个低频调用的全局优化线程
这样看来，针对上面的问题回答：ALOAM绝对是有后端的，没有回环检测就是了。

建图模块是scan-to-map的思路，对了，一般的配准都是有哪几种可以总结一下看看。
