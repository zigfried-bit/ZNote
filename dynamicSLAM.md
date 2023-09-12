[removert](https://github.com/irapkaist/removert#further-improvements)这个项目是先使用slam建图，然后去除地图中的动态物体
[LiDAR](https://github.com/PRBonn/LiDAR-MOS)这个项目是使用深度学习，剔除点云中的动态物体，将不含动态物体的点云作为slam的输入
LiDAR使用的网络有：
- [SalsaNext](https://github.com/TiagoCortinhal/SalsaNext)
- [RangeNet++](https://github.com/PRBonn/lidar-bonnetal)
- [MINet](https://github.com/sj-li/MINet)

### 1.quick test
1. 下载LiDAR_MOS_toy_dataset
2. 将LiDAR_MOS_toy_dataset解压放入LiDAR-MOS/data
3. 运行命令
```bash
$ cd mos_SalsaNext/train/tasks/semantic
$ python3 infer.py -d ../../../../data -m ../../../../data/model_salsanext_residual_1 -l ../../../../data/predictions_salsanext_residual_1_new -s valid
```
注意，推理的时候data/sequences/08/velodyne中的点云帧数目要与data/sequences/08/labels中的数目一致
4. 可视化需要安装[vispy](https://vispy.org/installation.html)
> VisPy requires at least one toolkit for opening a window and creates an OpenGL context. 

这里我们使用pyQt5作为Backend requirements
`$ pip3 install pyQt5`
然后运行
```bash
$ python3 utils/visualize_mos.py -d data -p data/predictions_salsanext_residual_1_valid -s 8  # here we use a specific sequence number
```
其中，data/sequences/08/velodyne中的点云帧数目要与data/predictions_salsanext_residual_1_valid/sequences/08/predictions中预测结果的帧数一致
5. 动态物体去除
运行：
`python3 utils/scan_cleaner.py`
报错：
```bash
Traceback (most recent call last):
  File "utils/scan_cleaner.py", line 36, in <module>
    data_yaml = yaml.load(open('config/semantic-kitti-mos.yaml'))
TypeError: load() missing 1 required positional argument: 'Loader'
```
原因：
> YAML 5.1版本后弃用了yaml.load(file)这个用法，因为觉得很不安全，5.1版本之后就修改了需要指定Loader，通过默认加载​​器（FullLoader）禁止执行任意函数，该load函数也变得更加安全

而当前PyYAML包的版本是6.0

解决：
```python
d1=yaml.load(file,Loader=yaml.FullLoader)
d1=yaml.safe_load(file)
d1 = yaml.load(file, Loader=yaml.CLoader)
```
注意，文件夹的名字必须是predictions_salsanext_residual_1_valid才行

运行结束后在data/sequences/08/clean_scans中会生成去除点云的数据帧

[ERASOR](https://github.com/LimHyungTae/ERASOR)并不是在SLAM过程中在线地滤除动态点云，而是在SLAM构建好点云地图后，对地图进行一遍后处理

### 2.保存aloam运行建立的地图
aloam运行建图，生成地图的话题为/laser_cloud_surround，16线lidar建图大概1.6帧左右
想要得到完整环境地图的pcd文件，在aloam建图的同时将话题录制下来然后使用bag_to_pcd得到相应的pcd文件
```bash
$ roslaunch aloam_velodyne aloam_velodyne_VLP_16.launch
$ rosbag record -o aloam_map.bag /laser_cloud_surround /aft_mapped_path
$ rosbag play 2023-04-29-09-36-39.bag --topic /rslidar_points
```
录制完成后
```bash
$ rosrun pcl_ros bag_to_pcd <bag_file> <topic> <location>
```


### 3.alom中加入imu 2023-6-5

1. 说某个刚体的位姿R和T，一定是基于某个坐标系C而言的，C可以是世界坐标系，也可以是上一帧的刚体坐标系。
2. 一个刚体的位姿为R和T的就是说，已知与该刚体固连的一点A在该刚体坐标系中的坐标，则A在C系中的坐标为RA+T

3. 另一种解释：如果我们把旋转只看作是向量的旋转，也就是只存在1个世界坐标系，pa是某个向量，pa绕某个坐标轴旋转某个角度后的新向量的坐标为：旋转矩阵R·pa

4. 左乘旋转矩阵绕固定坐标系旋转，右乘旋转矩阵绕自身坐标系旋转。这句话中"左乘起始对应3中的解释，右乘对应2中的解释"

5. 任何关于刚体旋转的旋转矩阵是由3个基本旋转矩阵复合而成
6. 对于在三维空间里的一个参考系，任何坐标系的取向，都可以用三个欧拉角来表现
7. 一组能够精确描述旋转的欧拉角, 可以如下描述：旋转角度(α,β,γ),旋转顺序(x->y->z),内旋
8. 我们常用的RPY角，实际上是旋转角度(roll,pitch,yaw),旋转顺序(x->y->z),外旋;外旋左乘，也就是R=Z(yaw)Y(pitch)X(roll)

9. 总结一下就是**RPY就是xyz外旋**

1先看一下IMU在loam中起的作用
需要测试一下，数据集哪里找？可以使用lego-loam的数据集试试

首先第一个问题：IMU输出的RPY是相对于哪个参考系的？
一般是2种情况，东北天和自身初始位姿
东北天一般是组合惯导，自身初始位姿则是指imu运行的第一帧位姿
loam中应该是后者。

不考虑lidar和imu之间固定的位姿变换R和T，使用imu也就相当于知道lidar的姿态

loam比较别扭的一点就是它使用z向前x向左y向上的坐标系作为全局坐标系，lidar和imu都要转化到这个坐标系下使用

**作用1**scanRegistration用于点云畸变校正

终于整明白了！

主要的误区在于，假设A是世界坐标系，A经过某种变换到B，B的全局位姿是Tb；B再通过某种变换到C，以B为基准C的位姿Tc，则C的全局位姿为：Tb×Tc
即，从A出发一路右乘，或者从C出发，一路左乘。
我主要陷在了"从A出发，一路左乘了"，没有为什么，就是这样。

再三提醒，B的位姿Tb代表，B的某固连点P的全局坐标为Tb×P

2023-6-6

开始之前的问题：
- IMU校正的作用如何体现？需要数据集测试，lego-loam的数据集或者kitti的
- **潜在的创新点**loam中是没有标定imu和lidar的

imu循环队列长度怎么设置？

1. timeScanCur 认为是一帧点云中第一个点的生成时间
也就是雷达驱动是先获取时间戳，后转换点云的

2. 为了求1个点的位姿，1/200秒的时间间隔，也要插值么？有必要么？

3. 涉及到yaw角范围的判断，暂时不研究欧拉角取值范围的问题

4. **潜在的创新点**在进行点的畸变校正过程中，是逐点校正的，一帧点云中，从第1个点到最后1个点，时间跨度长达0.1s
在这种情况下，imu数据不会更新，最新的imu数据也就是imuPointerLast，不会有更新的数据，一旦某个点的时间戳大于了imuPointerLast，就只能使用imuPointerLast作为该点的位姿和速度了
就是不知道这种情况对畸变校正有多大影响，也不知道大于imuPointerLast的点云有多少，理论上可以测试的
其次，大于的部分为什么要直接等于imuPointerLast，为啥不继续插值，时间间隔是已知的，这其中也有一些考虑吗？
还有就是位移也是线性插值，既然是匀加速，为什么不用非线性插值？

5. 终于把scanRegistration中的公式推完了，总结一下就是，scanRegistration中点云畸变校正补偿的是：由于一帧点云生成过程(0.1s)内加减速度引起的畸变，并没有补偿匀速运动产生的畸变
对于车辆来说，去除这种畸变，意义大吗？只去除匀速畸变是否够用？

6. 不标定imu和lidar的话，对scanRegistration的影响在于？
imu和lidar之间存在固定的R和T，在求由加速度引起的畸变时，其实是没有影响的

2023-6-7
laserOdometry并没有接收原始的imu消息，只接收了来自scanRegistration中的imu信息，还是以点云的形式发的，有点巧妙

1. scanRegistration消除的是点云由于加速度产生的畸变，在laserOdometry中点云帧匹配的过程中才考虑匀速运动的畸变
而且，在匹配过程中，imu仅仅提供了两帧平移向量的初值，但是这个初值的写法不太理解

2. 点云的匀速畸变去除TransformToStart实在是有点绕，确实是转到了start系下的，但是就是感觉别扭，把雷达和点云的关系一定理解好

2023-6-8

 彻底晕了
 匀速畸变去除到底是为什么呀？
