# IMU Lidar 标定
最初的想法是，寻找imu和lidar标定的方法，获得lidar和imu的位姿关系
一般将imu坐标系当作是车体坐标系，因此完成lidar和imu的标定，也就相当于完成了lidar和车体的标定

目前拟采用的方法是开源项目[imu_lidar_calibration](https://github.com/unmannedlab/imu_lidar_calibration)，这个方法的步骤如下：

## 1. Intrinsic Calibration of IMU
> First of all, we need to determine the noise characterisitcs of the IMU biases.

首先，IMU误差分析，包括陀螺仪的误差和加速度计的误差。项目给出以下几种方法：
- [kalibr_allan](https://github.com/rpng/kalibr_allan)
- [imu_utils](https://github.com/gaowenliang/imu_utils)
- [allan_variance_ros](https://github.com/ori-drs/allan_variance_ros)

第一种方法是matlab实现的，后两种方法基于ros，方便起见，试一下后两种方法，顺便做交叉验证。
### 1.1 imu_utils
使用方法：
- 准备imu静置2-3小时的.bag数据，消息类型`sensor_msgs/Imu`
- 编辑.launch文件，设置对应的imu话题名，bag包的回放时长(min)等，例如：
```xml
<launch>
    <node pkg="imu_utils" type="imu_an" name="imu_an" output="screen">
        <param name="imu_topic" type="string" value= "/djiros/imu"/>
        <param name="imu_name" type="string" value= "A3"/>
        <param name="data_save_path" type="string" value= "$(find imu_utils)/data/"/>
        <param name="max_time_min" type="int" value= "120"/>
        <param name="max_cluster" type="int" value= "100"/>
    </node>
</launch>

```

```bash
$ roslaunch imu_utils *.launch
$ rosbag play -r 200 *.bag
```
误差结果将被保存到data文件夹下，例如：
```yaml
%YAML:1.0
---
type: IMU
name: A3
Gyr:
   unit: " rad/s"
   avg-axis:
      gyr_n: 2.1372975748787017e-03
      gyr_w: 3.0268976891016541e-05
   x-axis:
      gyr_n: 2.2826785655367665e-03
      gyr_w: 3.6262088051679703e-05
   y-axis:
      gyr_n: 2.1481008562222203e-03
      gyr_w: 3.1988584957867144e-05
   z-axis:
      gyr_n: 1.9811133028771193e-03
      gyr_w: 2.2556257663502773e-05
Acc:
   unit: " m/s^2"
   avg-axis:
      acc_n: 2.7605678922214171e-02
      acc_w: 6.0581152332136806e-04
   x-axis:
      acc_n: 2.3276323617612848e-02
      acc_w: 5.3040579865760431e-04
   y-axis:
      acc_n: 2.4076335557805029e-02
      acc_w: 6.0358237557223962e-04
   z-axis:
      acc_n: 3.5464377591224637e-02
      acc_w: 6.8344639573426045e-04
```
值得注意的是，上面我跑出的结果与imu_utils作者给出的结果并不相同，在[Issues #11](https://github.com/gaowenliang/imu_utils/issues/11)中，有人也提到了相同的问题，而我与他们的结果反而是非常接近的。首先，不考虑代码本身的问题，可以使用另一种方法allan_variance_ros做一下验证。

### 1.2 allan_variance_ros
使用方法：
- 准备imu静置2-3小时的.bag数据，消息类型sensor_msgs/Imu
- 推荐重新按照时间戳组织ROS消息(直观的感觉是bag包变小了很多)
```shell
$ rosrun allan_variance_ros cookbag.py --input original_rosbag --output cooked_rosbag
```
- 计算imu的Allan方差，保存到csv文件中
```bash
$ rosrun allan_variance_ros allan_variance [path_to_folder_containing_bag] [path_to_config_file]
```
此处注意是bag包的文件夹，config文件的作用相当于imu_utils中launch文件的参数，例如：
```yaml
imu_topic: "/djiros/imu"
imu_rate: 400
measure_rate: 400 # Rate to which imu data is subsampled
sequence_time: 7200
```
上面就是A3的参数设置，为了与imu_utils对比，时间设为7200s，即120min
- 输出结果
```bash
$ rosrun allan_variance_ros analysis.py --data allan_variance.csv --config .yaml
```
在工作空间目录下，生成结果文件imu.yaml以及相应的图像
```yaml
#Accelerometer
accelerometer_noise_density: 0.002341215182545057 
accelerometer_random_walk: 0.00017952790007994538 

#Gyroscope
gyroscope_noise_density: 0.00011966407438672361 
gyroscope_random_walk: 6.519561709524828e-06 

rostopic: '/djiros/imu' 
update_rate: 400 
```
终端中会输出更详细的信息：
```bash
ACCELEROMETER:
X Velocity Random Walk:  0.00149 m/s/sqrt(s)  0.08927 m/s/sqrt(hr)
Y Velocity Random Walk:  0.00158 m/s/sqrt(s)  0.09469 m/s/sqrt(hr)
Z Velocity Random Walk:  0.00234 m/s/sqrt(s)  0.14047 m/s/sqrt(hr)
X Bias Instability:  0.00031 m/s^2  4031.14190 m/hr^2
Y Bias Instability:  0.00049 m/s^2  6325.38590 m/hr^2
Z Bias Instability:  0.00074 m/s^2  9599.27242 m/hr^2
X Accel Random Walk:  0.00005 m/s^2/sqrt(s)
Y Accel Random Walk:  0.00010 m/s^2/sqrt(s)
Z Accel Random Walk:  0.00018 m/s^2/sqrt(s)
GYROSCOPE:
X Angle Random Walk:  0.00686 deg/sqrt(s)  0.41134 deg/sqrt(hr)
Y Angle Random Walk:  0.00686 deg/sqrt(s)  0.41137 deg/sqrt(hr)
Z Angle Random Walk:  0.00610 deg/sqrt(s)  0.36615 deg/sqrt(hr)
X Bias Instability:  0.00172 deg/s  6.19424 deg/hr
Y Bias Instability:  0.00161 deg/s  5.79735 deg/hr
Z Bias Instability:  0.00110 deg/s  3.97113 deg/hr
X Rate Random Walk:  0.00037 deg/s/sqrt(s)
Y Rate Random Walk:  0.00032 deg/s/sqrt(s)
Z Rate Random Walk:  0.00019 deg/s/sqrt(s)
Writing Kalibr imu.yaml file.
Make sure to update rostopic and rate.
```
首先来分析一下终端输出与yaml文件的结果：
> Hello ROS community,
>
> I made a simple tool for computing IMU noise parameters: [GitHub - ori-drs/allan_variance_ros: ROS compatible tool to generate Allan Deviation plots](https://github.com/ori-drs/allan_variance_ros).
>
> It can take a rosbag of IMU data and in a few minutes compute the white noise sigma and bias random walk sigma, producing a yaml file compatible with [Kalibr](https://github.com/ethz-asl/kalibr).
>
> Unlike existing tools I use rosbag::View in C++ to efficiently open and iterate through a rosbag. Let me know your thoughts here or on the Github repo.
>
> Thanks!

上面是allan_variance_ros的作者russell_robot在社区开源项目时的描述，可以看到，生成的yaml文件是为了兼容kalibr，kalibr是一个视觉和惯导的标定工具箱。

imu的随机误差来源主要来自于两个，一个是高斯白噪声，一个是Bias随机游走。
- 加速度计的高斯白噪声：`accelerometer_noise_density` m/s/sqrt(s)
- 陀螺仪的高斯白噪声：`gyroscope_noise_density` rad/sqrt(s)

其中，加速度计的高斯白噪声是取`X/Y/Z Velocity Random Walk`的最大值；陀螺仪的高斯白噪声是取`X/Y/Z Angle Random Walk`最大值转换单位后得到。

- 加速度计的Bias随机游走：`accelerometer_random_walk` m/s^2/sqrt(s)
- 陀螺仪的Bias随机游走：`gyroscope_random_walk` rad/s/sqrt(s)

其中，加速度计的Bias随机游走是取`X/Y/Z Accel Random Walk`的最大值；陀螺仪的Bias随机游走是取`X/Y/Z Rate Random Walk`最大值转换单位后得到。

此外，imu随机误差的表示有连续和离散两种形式，allan_variance_ros输出的结果是连续形式。直观上的理解，采样频率越高，随机噪声应该越大，随机游走的应该越小。由此：
- 连续形式的高斯白噪声应**乘以**sqrt(Hz)得到其离散形式
- 连续形式的Bias随机游走应**除以**sqrt(Hz)得到其离散形式

比较imu_utils和allan_variance_ros两种方法的结果，发现就算是数量级也很难完全对应上，ROS社区中Martin_Guenther[分析](https://discourse.ros.org/t/open-source-allan-variance-tool-for-rosbags/23136/5)：
>Thank you for this tool, it’s amazing! I’ve been digging into the IMU calibration rabbit hole for the past week, and your package is a godsend.
>
>Many people seem to use [gaowenliang/imu_utils](https://github.com/gaowenliang/imu_utils) to estimate the IMU parameters, so that was my starting point as well. There are a couple of problems with that package though. Coupled with the fact that there are half a dozen synonyms for the IMU parameters used in datasheets and the literature, it took me way too long to figure all of that out:
> - It only prints out White Noise (N) and Bias Instability (B), but doesn’t print out Random Walk (K), even though it estimates it.
> - For white noise, it doesn’t actually give you N, but instead N⋅sqrt(f), where f is the IMU sampling frequency (in other words, it already converts the continuous-time noise density into a discrete-time standard deviation).
> - The units in the README are incorrect. The units seem to indicate that the estimated parameters are in fact continuous-time white noise (N) and random walk (K), but they are instead discrete-time white noise and bias instability (B). Nevertheless, there are plenty of tutorials online that recommend using those values from imu_utils to estimate N and K (even in the MYNTEYE SDK documentation).
> - The code littered with magic constants (57.3, 0.6642824703) without providing any explanation of what they are or how they are derived, and it constantly switches units internally between degrees, rad, seconds and hours, and any combination of them.
> - The whole package is basically undocumented. It also sometimes uses the variable names Q, N, B, K and R for something slightly different than what they mean in the literature, so you need a conversion to get the real value of those parameters.

很关键的一点在于，他提到了imu_utils输出的形式可能比较混乱，README文档也没有说得清楚。

### 1.3 对比分析
通过上面试验和分析，目前需要解决如下几个问题：
1. 为什么上面我跑出的结果与imu_utils作者给出的结果并不相同？
2. allan_variance_ros和imu_utils能不能相互验证？
3. imu_utils的结果是否不可用？

对于第一个问题，受Martin_Guenther分析的启发，考虑到imu误差的连续、离散两种形式，可以发现，对于高斯白噪声，作者的给出的数据是连续形式，而我跑出的结果是离散形式。Bias随机游走则都是连续形式。

对于第二个问题，解决了问题1，在连续形式下看两种方法的结果，数量级可以对应上，但是有些数值的差别还是比较明显。

对于第三个问题，理解了输出结果的单位后，应该说imu_utils的结果还是可用的。


## 2. Data collection for extrinsic calibration
> We need to excite all degrees of freedom during collecting data required for extrinsic calibration.

收集imu和lidar的标定数据，需要imu绕各个坐标轴进行充分的旋转

## 3. Inter-sensor rotation estimation
这一步主要利用手眼标定，得到imu和lidar的旋转关系
主要步骤是：
- 修改`ros_calib_init.launch`文件中的lidar、imu话题名，bag包名，优化的最大帧数，imu高斯白噪声，结果文件的保存位置。
- `roslaunch linkalibr ros_calib_init.launch`

## 4. Inter-sensor translation estimation
上一步的旋转关系，只需要对imu的陀螺仪状态进行积分估计即可，平移向量的估计则需要对imu的运动状态进行计算。
主要步骤是：
- 修改`linkalibr_ouster_vectornav.launch`文件中的lidar、imu话题名，bag包名，imu高斯白噪声，Bias随机游走，结果文件的保存位置。
- `roslaunch linkalibr linkalibr_ouster_vectornav.launch`

