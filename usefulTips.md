## 编程常用技巧

### 1 git
如果github有一个远程仓库，win下有一个本地仓库A，linux下有一个本地仓库B

如果A做了修改，推送到了远程仓库中，此时B如果也作了修改，推送之前需要运行

$ git fetch origin main

$ git merge origin FETCH_HEAD

与远程仓库同步，后再推送

### 2 cv::Mat和ROS图像互相转换
[link](https://blog.csdn.net/weixin_44684139/article/details/108759860)

### 3 fstream处理文本文件

