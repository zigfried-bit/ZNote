# Daily Notes

> By Zigfried

### 2021.11.16

**整理收藏夹**

#### 1、WSL安装相关

1.1 WSL安装Ubuntu16.04 LTS

> 参考[Azure's Blog]([wsl安装Ubuntu16.04+Python2.7 - azureology - 博客园 (cnblogs.com)](https://www.cnblogs.com/azureology/p/12896758.html))

任意路径打开PowerShell，输入以下命令：

```powershell
Invoke-WebRequest -Uri https://aka.ms/wsl-ubuntu-1604 -OutFile Ubuntu.appx -UseBasicParsing
Add-AppxPackage .\Ubuntu.appx
```

然后就可以 `WIN`+`Q` 直接搜索到Ubuntu 16.04 LTS应用了

打开设置UNIX账号密码即安装完成

在资源管理器左侧导航窗格也会出现Linux的:penguin:（Win11）

换源可以在[清华大学开源软件镜像站](https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/)找到相应版本的软件镜像源

Ubuntu 的软件源配置文件是 `/etc/apt/sources.list` 

将系统自带的该文件做个备份，将该文件替换为镜像源内容即可



1.2 在WSL Ubuntu 16.04 LTS 安装ROS时遇到问题

> 参考[知乎@裕如](https://zhuanlan.zhihu.com/p/428043975)的方法

在*1.3 Set up your keys*时

```
curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | sudo apt-key add -
```

出现错误

> gpg : 找不到有效的 OpenPGP 数据

解决方法：

```
wget http://packages.ros.org/ros.key
sudo apt-key add ros.key
```



1.3 rosdep问题

> 参考[知乎@不解言说](https://zhuanlan.zhihu.com/p/392082731)、[鱼香ROS](http://fishros.com/#/fish_home)

两种方法：16.04（改文件）20.04（rosdepc）



#### 2、[Trendy Gwentleman](https://trendygwentleman.com/cosmetics/)

> 昆特牌头像、边框、称号、卡背、领袖皮肤、战场和硬币大全！

---



#### 3、LeetCode第一题 TwoSum

[参考答案](https://blog.csdn.net/sun20082567/article/details/31417001)

---



#### 4、深度学习论文

[论文链接](https://www.cnblogs.com/sxron/articles/5996953.html)



#### 5、scikit-learn

[官网](https://scikit-learn.org/stable/index.html)

[相关资料1](https://www.cnblogs.com/geo-will/p/10468253.html)

[相关资料2](https://www.cnblogs.com/zackstang/p/12316640.html)

---



#### 6、LaTeX教程

[简明教程](https://www.bilibili.com/video/BV15b411j7Au?p=6)

---



#### 7、[CS:APP代码](http://csapp.cs.cmu.edu/3e/code.html)

---



#### 8、inkscape

[官方教程](https://inkscape.org/doc/tutorials/basic/tutorial-basic.html)

---



#### 9、Windows安装node.js

> 参考[后山de小猿](https://my.oschina.net/hshdexy/blog/4542419?hmsr=kaifa_aladdin)

**注意** 教程中:

`npm config set perfix "D:\Program Files\nodejs\node_global"`写错了，应为`prefix`

---

#### 10、[Hexo](https://hexo.io/zh-cn/)

> 博客框架

---



#### 11、[ProtoBuf简介](https://www.jianshu.com/p/a24c88c0526a)



#### 12、[Licenses by Name](https://opensource.org/licenses/alphabetical)

> Open Source Initiative



#### 13、git相关

[廖雪峰教程](https://www.liaoxuefeng.com/wiki/896043488029600/896954848507552)

[git官网](https://git-scm.com/docs)



### 2021.11.17

#### 1、WSL安装使用zsh

> 参考简书[@疯刘](https://www.jianshu.com/p/02feec629fb0)

`cat /etc/shells` : 显示存在的shell

`chsh -s /bin/zsh` : 将zsh设为默认shell

`chsh -s /bin/bash` : 切换回bash

`exit` : 可以在两者之间任意切换



### 2021.11.19

#### 1、ln命令用法

在bash和zsh中，输入python，显示command not found

其实环境里是装了python2和python3的

这时候应该需要一个符号链接

- `sudo ln -d <source file> <target file>`

  硬链接必须在同一个文件系统中，且硬链接不能指向目录（只适用于超级用户）

- `sudo ln -s <source file> <target file>`

  符号链接可以不必在同一个文件系统中

- `rm -f <sysbolic_name>`

  强行删除任何已存在的目标文件链接

用 `whereis python3` 确定/usr/bin/python3路径下确实有python3

建立符号链接，将python链接到python3上

`sudo ln -s /usr/bin/python3 /usr/bin/python`

这下，不论是bash还是zsh都可以使用python了，和python3完全等同。



#### 2、关于.sh和.zsh

因为在.zshrc中添加了

```shell
source /opt/ros/noetic/setup.zsh
source /home/zigfried/catkin_ws/devel/setup.zsh
```

成功启动了ROS master，让我以为在.zshrc中只能添加.zsh的文件

结果手动安装autojump插件时，填了一行

```shell
[[ -s /home/zigfried/.autojump/etc/profile.d/autojump.sh ]] && source /home/zigfried/.autojump/etc/profile.d/autojump.sh
```

结果还能source成功，而且source后就可以正常使用了

上面这一行应该也可以添加到.bashrc中



#### 3、常用命令

- `cat /proc/version` 查看系统版本

- `cat /etc/shells` 查看系统中安装了哪几种终端程序

- `chsh -s /bin/bash` 切换默认终端

- `printenv | grep SHELL` 查看系统默认终端

- `echo $SHELL` 查看系统默认终端

- `file /bin/sh` /bin/sh: symbolic link to dash 

这说明Ubuntu20.04的sh默认指向dash

> Dash，GNU/Linux操作系统中的/bin/sh本是bash (Bourne-Again Shell) 的符号链接，但鉴于bash过于复杂，有人把bash从NetBSD移植到Linux并更名为dash (Debian Almquist Shell)，并建议将/bin/sh指向它，以获得更快的脚本执行速度。
>
> Dash Shell 比Bash Shell小的多，符合POSIX标准。也就是若脚本第一行为“#!/bin/sh”，我们使用命令：”sh script_name.sh“时是调用的dash去解释脚本；
>
> Ubuntu继承了Debian，所以从Ubuntu 6.10开始默认是Dash Shell。

### 2021.11.20

#### 1、关于CPU

**精简指令集**（Reduced Instruction Set Computing, RISC）

这种 CPU 的设计中，微指令集较为精简，每个指令的运行时间都很短，完成的动作也很单纯，指令的执行效能较佳； 但是若要做复杂的事情，就要由多个指令来完成。

- Sun: SPARC系列 > 学术领域大型工作站，银行金融体系主服务器
- IBM: Power Architecture(包括PowerPC)系列 > Sony PS3(PowerPC架构的Cell处理器)
- ARM系列 > 手机、PDA、导航系统、网络设备（交换机、路由器）

**复杂指令集**（Complex Instruction Set Computer, CISC）

与 RISC 不同，CISC 在微指令集的每个小指令可以执行一些较低阶的硬件操作，指令数目多而且复杂， 每条指令的长度并不相同。因为指令执行较为复杂所以每条指令花费的时间较长， 但每条个别指令可以处理的工作较为丰富。

- AMD: x86架构
- Intel: x86架构
- VIA: x86架构

不同x86架构的CPU的差异主要是微指令集的不同。

CPU频率 = 外频 × 倍频

外频是CPU与外部组件进行数据传输时的速度，倍频则是CPU内部用来加速工作效能的一个倍数。

较常被超频的是外频，倍频一般出厂时被锁定而无法修改

理论上CPU与主存储器的外频应该相同才好。

---

#### 2、关于Linux

> 在Linux中，每个装置都被当成一个档案来对待

几乎所有的硬件装置档案都在/dev这个目录内

- SATA(Serial Advanced Technology Attachment)，SATA(Serial ATA)硬盘，又称串口硬盘

- 小型计算机接口(Small Computer System Interface)
- USB(Universal Serial Bus)，通用串行总线
- IDE(Integrated Drive Electronics) 也叫ATA接口

由于SATA/USB/SCSI等磁盘接口都是使用SCSI模块来驱动的，因此这些接口的磁盘装置文件名都是 `/dev/sd[a-p]` 的格式。但与IDE接口不同，SATA/USB接口的磁盘没有一定的顺序，要根据Linux核心侦测到的磁盘顺序决定装置文件名

>例题：
>
>如果你的 PC 上面有两个 SATA 磁盘以及一个 USB 磁盘，而主板上面有六个 SATA 的插
>槽。这两个 SATA 磁盘分别安插在主板上的 SATA1, SATA5 插槽上， 请问这三个磁盘在
>Linux 中的装置文件名为何？
>
>答：
>
>由于是使用侦测到的顺序来决定装置文件名，并非与实际插槽代号有关，因此装置的文件
>名如下：
>
>1、SATA1插槽上的档名：/dev/sda
>
>2、SATA1插槽上的档名：/dev/sda
>
>3、USB磁盘：/dev/sdc 开机完成才被系统捉到

**文件系统与目录树的关系**（挂载）

Linux操作系统将所有的设备都看作文件，它将整个计算机的资源都整合成一个大的文件目录。我们要访问存储设备中的文件，必须将文件所在的分区**挂载**到一个已存在的目录上，然后通过访问这个目录来访问存储设备。

由于整个Linux系统最重要的是根目录，因此根目录一定需要挂载到某个分割槽。

---

几个常用指令

- `date` 显示日期
- `cal` 显示日历
- `bc` 计算器
- `exit` & `Ctrl+D` 离开终端

**man page**

- `man` 系统手册页 不同手册根据代号表示不同内容
  - 1 用户在shell环境中可以操作的指令或可执行文件
  - 5 配置文件或者某些档案的格式
  - 8 系统管理员可用的管理指令

`man` 手册的使用方法：

- space & [Page Down] 向下翻页
- [Page Up] 向上翻页
- [Home] 去到第一页
- [End] 去到最后一页
- /string 向下搜寻"string"
- ?string 向上搜寻"string"
- q 退出手册
- `man -f` 指令 `whatis`
- `man -k` 关键字 `apropos`

**info page**

和man page用途差不多，info将文件数据拆成一个一个段落，可以想成文本模式的网页显示数据

**/usr/share/doc**

也有很多说明文档

**nano**

超简单文本编辑器

[Ctrl]+W: 搜索字符串

[Ctrl]+X: 退出

**观察系统使用状态**

- `who` 目前有谁在线
- `netstat -a` 网络联机状态
- `ps -aux` 背景执行的程序
- `sync` 数据同步写入磁盘
- `shutdown` 关机指令
- `reboot` 重新启动
- `poweroff` 关机
- `halt` 关机
- `init 0` 关机

#### 3、catkin_make中的vtk问题

编译chapter3_tutorials时遇到如下问题

```shell
-- The imported target "vtkParseOGLExt" references the file
   "/usr/bin/vtkParseOGLExt-7.1"
but this file does not exist.  Possible reasons include:
* The file was deleted, renamed, or moved to another location.
* An install or uninstall procedure did not complete successfully.
* The installation package was faulty and contained
   "/usr/lib/cmake/vtk-7.1/VTKTargets.cmake"
but not all the files it references.

-- The imported target "vtkRenderingPythonTkWidgets" references the file
   "/usr/lib/x86_64-linux-gnu/libvtkRenderingPythonTkWidgets.so"
but this file does not exist.  Possible reasons include:
* The file was deleted, renamed, or moved to another location.
* An install or uninstall procedure did not complete successfully.
* The installation package was faulty and contained
   "/usr/lib/cmake/vtk-7.1/VTKTargets.cmake"
but not all the files it references.

-- The imported target "vtk" references the file
   "/usr/bin/vtk"
but this file does not exist.  Possible reasons include:
* The file was deleted, renamed, or moved to another location.
* An install or uninstall procedure did not complete successfully.
* The installation package was faulty and contained
   "/usr/lib/cmake/vtk-7.1/VTKTargets.cmake"
but not all the files it references.

-- The imported target "pvtk" references the file
   "/usr/bin/pvtk"
but this file does not exist.  Possible reasons include:
* The file was deleted, renamed, or moved to another location.
* An install or uninstall procedure did not complete successfully.
* The installation package was faulty and contained
   "/usr/lib/cmake/vtk-7.1/VTKTargets.cmake"
but not all the files it references.
```

由于vtk本身不存在，而vtk7存在，通过建立vtk与vtk7的软链接，成功解决第三个问题。

`sudo ln -s /usr/bin/vtk7 /usr/bin/vtk`

**在真实的Ubuntu16.04系统中，由于安装的是vtk6.2，上述文件都可以找到，因此并没有遇到以上问题！**

---

问题其实出在`"/usr/lib/cmake/vtk-7.1/VTKTargets.cmake"` 这个文件中！

**发现：**我从vtk官网下载的vtk7.1.1安装包中的cmake/vtk-7.1中的`VTKTargets.cmake`与现在目录中的不一致！可能上面的软链接都已经不需要了！

**替换：**`sudo cp -r vtk-7.1 /usr/lib/cmake/`

**结果：**简单地替换显然是不行的，出现cmake编译错误，新的vtk-7.1在lib中需要新的文件

*暂时先到这儿，不瞎折腾了，虽然有如上问题，但暂时没有无法编译等大问题，可以使用*

2021.11.21 17: 33 



### 2021.11.21

#### 1、WSL python2安装pip20.3.4

由于WSL Ubuntu20.04下的python2没有pip包，而python2的pip有时还会用到，因此需要单独安装。

pip21已经不支持python2了，因此在官网下载最后支持python2的[pip20.3.4](https://pypi.org/project/pip/20.3.4/#files)

首先下载并安装setuptools，因为后面安装会用到

```shell
wget --no-check-certificate https://bootstrap.pypa.io/ez_setup.py
sudo python ez_setup.py --insecure
```

然后手动下载pip-20.3.4.tar.gz安装包，放在home中

```shell
tar -xf pip-20.3.4.tar.gz
cd pip-20.3.4
sudo python setup.py install
```

执行结束后，pip2就可以使用了。

#### 2、关于Liunx 文件属性

`ls -al` 列出所有档案详细的权限与属性（包含隐层文件）

```shell
root@DESKTOP-AKFJSRG:~# ls -al
total 44
drwx------  7 root root 4096 Nov 17 11:13 .
drwxr-xr-x 19 root root 4096 Nov 21 11:25 ..
-rw-------  1 root root  241 Nov 17 11:16 .bash_history
-rw-r--r--  1 root root 3106 Dec  5  2019 .bashrc
drwx------  6 root root 4096 Nov  9 20:34 .cache
drwx------  6 root root 4096 Nov  9 20:02 .config
drwx------  3 root root 4096 Nov  9 20:02 .dbus
drwxr-xr-x  3 root root 4096 Nov  9 20:02 .local
-rw-r--r--  1 root root    0 Nov 21 16:21 .motd_shown
-rw-r--r--  1 root root  161 Dec  5  2019 .profile
drwx------  2 root root 4096 Nov 17 11:15 .ssh
-rw-------  1 root root 3271 Nov  9 19:57 .viminfo
```

- 第一栏代表这个档案的类型与权限(permission)
- 第一栏有10个字符
- 第一个字符代表这个档案是目录、档案或链接文件等：
  - `d` 目录
  - `-` file
  - `l` Link file
  - `b` 可随机存取装置
  - `c` 一次性读取装置，如键盘、鼠标
- 接下来的字符，以三个为一组，均为 `rwx` ，分别代表，可读，可写，可执行，三个权限位置不变，如果没有权限，就会出现减号 `-` 
  - 第一组为"档案拥有者权限"
  - 第二组为"同群组的权限"
  - 第三组为"其它非本群组的权限"

---

- 第二栏表示有多少档名连结到此节点(i-node)
- 第三栏表示这个档案的"拥有者账号"
- 第四栏表示这个档案的所属群组
- 第五栏为这个档案的容量大小，默认单位为bytes
- 第六栏为这个档案的建档日期或者最近的修改日期
- 第七栏为这个档案的档名

> 进入目录需要可执行权限 `x` ，写入目录需要写入权限 `w`

**改变文件属性与权限**

- `chgrp` 改变档案所属群组
  - `chgrp [-R] GROUP FILE`
- `chown` 改变档案拥有者
  - `chown [-R] [OWNER][:[GROUP]] FILE`
  - 当复制档案给其他人时，修改拥有者或群组才能让他人使用档案
- `chmod` 改变档案的权限，SUID，SGID，SBIT等特性
  - 数字类型改变档案权限
    - r: 4
    - w:2
    - x: 1
    - `chmod [-R] xyz FILE/Dir`
  - 符号类型改变档案权限
    - u: user
    - g: group
    - o: others
    - a: all
    - `chmod u=rwx,go=rx .bashrc` ","中间无空格！
    - `chmod a-x .bashrc`

> 档案是否能被执行，由其是否具有 `x` 权限来决定，跟档案名没有绝对关系

档案的rwx，主要针对的是档案的内容，w不代表可以删除档案本身的权限

**权限对目录的重要性**

- r （read contents in directory）

读取目录结构列表的权限

- w（modify contents of directory）
  - 建立新的档案与目录
  - 删除已经存在的档案和目录（**不论该档案的权限**）
  - 将已存在的档案或目录进行更名
  - 搬移该目录内的档案、目录的位置
- x（access directory）

用户能否**进入(cd)**该目录成为工作目录，工作目录就是当前所在目录，如home

### 2021.11.22

#### 1、关于Linux 文件属性 续

**档案种类**

- 正规档案（regular file）`-`
  - 纯文本档 -可以`cat`
  - 二进制文件binary  指令`cat`就是一个binary file
  - 数据格式文件data file`last`指令可以读出

```shell
❯ last wtmp
wtmp begins Sat Nov 20 21:10:02 2021
```

- 目录 `d`
- 连结档（link）`l` 类似Windows的快捷方式
- 设备与装置文件device 与系统周边及储存等相关的档案，通常集中在/dev目录下
  - 区块(block)设备档 `b` 储存数据，以提供系统随机存取的接口设备，如硬盘
  - 字符(character)设备文件 `c` 串口端口的接口设备，如键盘鼠标
- 资料接口文件sockets `s` 通常被用在网络上的数据承接，最常在/var/run这个目录看到
- 数据输送文件(FIFO, pipe) `p` 主要解决多个程序同时存取一个档案所造成的错误问题

> socket与FIFO档案与程序(process)比较有关系

---

**Linux档案扩展名**

> 基本上，Linux的档案时没有所谓的扩展名的。能不能执行与权限有关，与文件名无关

不过可以通过扩展名了解档案的种类

- `*.sh` shell写成的scripts
- `*.Z`,`*.tar`,`*/tar.gz`,`*.zip`,`*.tgz` 压缩文件
- `*.html`,`*.php` 网页相关文档

**Linux档案长度限制**

使用预设的Ext2/Ext3文件系统时，针对档案的档名长度限制为：

- 单一档案或目录的最大容许文件名为255个字符；
- 包含完整路径名称及目录完整档名为4069个字符

文件名最好避免`* ? < > ; & ! [] | \ ' " () {}`，开头避免`+ -`

**Linux目录配置的依据**

Linux目录配置遵循FHS(Filesystem Hierarchy Standard)标准

FHS依据文件系统使用的频繁与否、是否允许使用者随意改动，而将目录定义成为4种交互作用的形态：

- shareable 可以分享给其它系统**挂载**使用的目录
- unshareable 自己机器上运作的装置档案或者是程序有关的socket档案
- static 函式库、文件说明文件、系统管理员所管理的主机服务配置文件
- variable 登录文件、一般用户可自行接受的新闻组

事实上，FHS针对目录树架构仅定义出3个目录下该放什么数据

- `/` root, 根目录: 与开机系统有关
- `/usr` unix software resource: 与软件安装/执行有关；
- `/var` variable: 与系统运作过程有关

**根目录**是整个系统最重要的一个目录，FHS标准建议，根目录所在的分割槽越小越好，且应用程序所安装的软件最好不要与根目录放在同一个分割槽内，保持根目录越小越好。

FHS定义根目录应该有如下此目录存在：

- `/bin`
- `/boot`
- `/dev`
- `/etc`
- `/home`
- `/lib`
- `/media`
- `/mnt`
- `/opt`
- `/root`
- `/sbin`
- `/srv`
- `/tmp`

其它一些需要了解的次目录

- `/lost+found`
- `/proc`
- `/sys`

**注意**：因为根目录与开机有关，开机过程中仅有根目录会被挂载，其它分割槽则是在开机完成之后才会持续地进行挂载行为，因此，根目录下与开机过程有关的此目录，不能与根目录放到不同的分割槽：

- /etc：配置文件
- /bin：重要执行档
- /dev：所需要的装置档案
- /lib：执行档所需函式库与核心所需的模块
- /sbin：重要的系统执行文件

#### 2、conda cmd&PowerShell

Windows PowerShell对conda命令的支持很不好，最好使用cmd

`conda env list` 列出当前环境

`conda activate <env>` 进入指定环境

#### 3、waterRPA

在python38环境下运行`python waterRPA.py`

可以想一些有用的功能，最好是流程比较长的，或者是可重复的

1. Github Desktop push

使用Github Desktop push时比较烦，可以让它自动push几分钟，说不定就成功了呢

2. SwitchHosts

使用Github520刷新hosts地址也比较烦，像刷新了可以运行一下（已成功2021.11.24 9:35）

全都和Github有关🤣

3. Gwent

用steam启动昆特牌有点烦，也可以尝试（已成功2021.11.22 23:50）

 ### 2021.11.23

#### 1、关于rqt_logger_level

rqt_logger_level让我把窗口拖出去之后，再也弄不会来了，卸载了重装也没用

直接是不能使用了，好在rqt整个的窗口还可以用，问题倒也不大，但的确目前没找到解决方法。

这其实也算WSL上ROS的一个Bug。有空把ROS卸掉重装一下试试，如果还不行，那就只能重置WSL了呀，希望不要走到那一步！我的ZSH啊，全都得推到重来😭

考虑到彻底卸载ROS很麻烦，重置再走一遍，也未尝不可，没重装10来次，这么学LINUX啊🤣

#### 2、WSL2 重置之路

- 换软件源 `/etc/apt/sources.list`

没有sublime和gedit有点难受啊，得用vim或者nano

还是清华镜像源，把https的s去掉

```
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse

# 预发布软件源，不建议启用
# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
```

不备份了就，打开看看放在好了

```
# See http://help.ubuntu.com/community/UpgradeNotes for how to upgrade to
# newer versions of the distribution.
deb http://archive.ubuntu.com/ubuntu/ focal main restricted
# deb-src http://archive.ubuntu.com/ubuntu/ focal main restricted

## Major bug fix updates produced after the final release of the
## distribution.
deb http://archive.ubuntu.com/ubuntu/ focal-updates main restricted
# deb-src http://archive.ubuntu.com/ubuntu/ focal-updates main restricted

## N.B. software from this repository is ENTIRELY UNSUPPORTED by the Ubuntu
## team. Also, please note that software in universe WILL NOT receive any
## review or updates from the Ubuntu security team.
deb http://archive.ubuntu.com/ubuntu/ focal universe
# deb-src http://archive.ubuntu.com/ubuntu/ focal universe
deb http://archive.ubuntu.com/ubuntu/ focal-updates universe
# deb-src http://archive.ubuntu.com/ubuntu/ focal-updates universe

## N.B. software from this repository is ENTIRELY UNSUPPORTED by the Ubuntu
## team, and may not be under a free licence. Please satisfy yourself as to
## your rights to use the software. Also, please note that software in
## multiverse WILL NOT receive any review or updates from the Ubuntu
## security team.
deb http://archive.ubuntu.com/ubuntu/ focal multiverse
# deb-src http://archive.ubuntu.com/ubuntu/ focal multiverse
deb http://archive.ubuntu.com/ubuntu/ focal-updates multiverse
# deb-src http://archive.ubuntu.com/ubuntu/ focal-updates multiverse

## N.B. software from this repository may not have been tested as
## extensively as that contained in the main release, although it includes
## newer versions of some applications which may provide useful features.
## Also, please note that software in backports WILL NOT receive any review
## or updates from the Ubuntu security team.
deb http://archive.ubuntu.com/ubuntu/ focal-backports main restricted universe multiverse
# deb-src http://archive.ubuntu.com/ubuntu/ focal-backports main restricted universe multiverse

## Uncomment the following two lines to add software from Canonical's
## 'partner' repository.
## This software is not part of Ubuntu, but is offered by Canonical and the
## respective vendors as a service to Ubuntu users.
# deb http://archive.canonical.com/ubuntu focal partner
# deb-src http://archive.canonical.com/ubuntu focal partner

deb http://security.ubuntu.com/ubuntu/ focal-security main restricted
# deb-src http://security.ubuntu.com/ubuntu/ focal-security main restricted
deb http://security.ubuntu.com/ubuntu/ focal-security universe
# deb-src http://security.ubuntu.com/ubuntu/ focal-security universe
deb http://security.ubuntu.com/ubuntu/ focal-security multiverse
# deb-src http://security.ubuntu.com/ubuntu/ focal-security multiverse
```

把原来的都注释了，后面加上清华的镜像源

可以准备安装ROS了

- 安装ROS noetic

第1步

```shell
$ sudo sh -c '. /etc/lsb-release && echo "deb http://mirrors.tuna.tsinghua.edu.cn/ros/ubuntu/ `lsb_release -cs` main" > /etc/apt/sources.list.d/ros-latest.list'
```

Setup my computer to accept software from packages.ros.org.

第2步

```shell
$ sudo apt install curl
$ curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | sudo apt-key add -
```

这一步当时在WSL Ubuntu16.04上费了好一番功夫，在20.04就直接OK

第3步

```shell
$ sudo apt update
$ sudo apt install ros-noetic-desktop-full
```

直接装个完整版3715MB，有点大

各种软件包，这下可装全了

装完7.5G，和说好的不一样呐😱

第4步

source环境

```shell
$ echo "source /opt/ros/noetic/setup.bash" >> ~/.bashrc
$ source ~/.bashrc
```

/noetic对任何用户甚至其他人都是可执行的，意味着里面的文件任何人都可以修改，所以不需要sudo就能更改setup.bash的内容

这里我才发现，官网早已给出了zsh的方案，现在我能理解l

哎，到这里，看一下用户目录多了哪些东西

多了

- .bashrc_history 可读可写 保存使用过的命令
- .sudo_as_admin_successful 可读可写 啥也没有
- .viminfo 可读可写 vim的使用记录

第5步

rosdep！

rosdep是负责自动安装一些包的依赖的，挺重要的

原来20.04才开始用`python3-rosdep` ，以前的版本都是python2

怪不得python27文件夹啥也没有呢，看了一下东西全在python3的文件夹下

先按照要求装上

```shell
$ sudo apt install python3-rosdep python3-rosinstall python3-rosinstall-generator python3-wstool build-essential
$ sudo rosdep init
$ rosdep update
```

果然，最后一步还是有问题，求助rosdepc吧

```shell
$ curl http://fishros.com/tools/install/rosdepc | bash 
```

一行代码解决，果然rosdepc就是为20.04量身设计的！

至此，ROS安装结束！

- 接下来试试编译chapter3_tutorials

这时候cmake里面已经有vit-7.1了，看看VTKTargets.cmake这个文件，还是按个问题啊，哎！不管了

用户目录多了个.ros文件夹，竟然是drwxrwxrwx！里面有个rosdep文件夹，还有一个rospack_cache的文件，里面存的是所有ros软件包的路径；rosdep中是.pickle文件，还有一个index文件，里面是rosdepc使用的网站，GitHub不行改到了Gitee

以后.ros里面还可能会有日志文件

创建个工作空间

```shell
$ mkdir -p ~/catkin_ws/src
$ cd ~/catkin_ws/
$ catkin_make
$ echo "source /home/zigfried/catkin_ws/devel/setup.bash" >> ~/.bashrc
$ source ~/.bashrc
```

放入chapter3_tutorials，删掉devel和build，开始编译

除了熟悉的vtk问题，其它啥问题没有，直接编译通过，full版就是不一样啊！

对了，小乌龟和rqt还没试呢，怎么能叫安装完成？

rqt_logger_level终于重置了，我再也不乱动它了

还是和以前一样，小乌龟的info没有在rqt_console中显示

以后尽量用rqt

一通操作，用户目录下多了好多东西

- `.cache` rwx 和字体配置、图形、着色有关，不知道什么用，应该是编译chapter3_tutorials时产生的
- `.config` rwx 目前只有rqt的配置文件，应该是各种配置文件，会不会原来把这个删掉，rqt就会好了吧😱
- `.dbus` rwx 数据总线，以后会用到

---

- 装个sublime吧

```shell
$ wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | sudo apt-key add -
$ sudo apt-get install apt-transport-https
$ echo "deb https://download.sublimetext.com/ apt/stable/" | sudo tee /etc/apt/sources.list.d/sublime-text.list
$ sudo apt-get update
$ sudo apt-get install sublime-text
```

update可能要多试几次，要不找不到软件包

差不多完事了！

### 2021.11.24

运行过小乌龟后，.ros中就出现了ROS日志文件log，记录了ROS运行时的各种日志消息

又多了一个rospack_cache文件，文件内容差不多

还多了一个roscore的记录，现在里面是1948，可能是记录每次rosmaster的进程编号

### 2021.11.29

#### 1、关于Linux文件属性 续

- /usr的内容与意义

usr不是user的缩写，而是Unix Software Resource的缩写，/usr里面放置的数据属于可分享与不可变动的，不是用户的数据，而是Unix操作系统软件资源，所有系统默认的软件都会放置到/usr下。

- /var的意义与内容

/var是在系统运行后才会逐渐占用硬盘容量的目录，因为/var目录主要针对常态性变动的档案，包括cache以及某些软件运作所产生的档案

等有空专门在16.04上再对照看一遍2021.11.30

- `.`代表当前目录，也可以使用`./`表示
- `..`代表上一层目录，也可以使用`../`来表示

`uname -r`: 查看Linux核心版本

`lsb_release -a`: 查看Distribution基本信息
