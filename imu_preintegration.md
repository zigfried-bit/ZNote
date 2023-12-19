### 2. 两空间向量的四元数

内积：包含两向量之间的夹角 $\theta$
$$ \boldsymbol {a} \cdot  \boldsymbol {b} 
=  \boldsymbol {a}^{\mathrm{T}} \boldsymbol {b}
= |a||b|\cos{\langle \boldsymbol {a} , \boldsymbol {b} \rangle}$$
外积：两向量之间的旋转轴
$$ \boldsymbol {a} \times  \boldsymbol {b} = 
\left | \begin{matrix}
\boldsymbol {e_1} &\boldsymbol {e_2}   & \boldsymbol {e_3} \\
a_1 &a_2 & a_3  \\
b_1 &b_2 & b_3 \\
\end{matrix} \right | =
\left | \begin{matrix}
0 &-a_3   & a_2 \\
a_3 &0 & -a_1  \\
-a_2 &a_1 & 0 \\
\end{matrix} \right | \boldsymbol {b} \overset{\mathrm{def}}{=}
\boldsymbol {a^\wedge} \boldsymbol {b}
$$
$$
|\boldsymbol {a} \times  \boldsymbol {b}|=
|a||b|\sin{\langle \boldsymbol {a} , \boldsymbol {b} \rangle}
$$
任何向量都对应着唯一一个反对称矩阵

**旋转矩阵**
绕x轴
$$
\begin{pmatrix}
1 & 0 & 0 \\
0 & \cos \theta_x & -\sin \theta_x \\
0 & \sin \theta_x & \cos \theta_x
\end{pmatrix}
$$
绕y轴
$$
\begin{pmatrix}
\cos \theta_y & 0 & \sin \theta_y \\
0 &1 &0 \\
-\sin \theta_y & 0 & \cos \theta_y
\end{pmatrix}
$$
绕z轴
$$
\begin{pmatrix}
\cos \theta_z & -\sin \theta_z & 0 \\
\sin \theta_z & \cos \theta_z & 0 \\
0 & 0 &1
\end{pmatrix}
$$
$旋转矩阵\Leftrightarrow行列式为1的正交矩阵$

n维旋转矩阵的集合可以定义为：
$$
\mathrm{SO}(n)=\{ \boldsymbol{R}\in \mathbb{R}^{n \times n}
| \boldsymbol{RR}^{\mathrm T}=\boldsymbol{I},
\mathrm{det}(\boldsymbol{R})=1 \}
$$
SO(n)意为特殊正交群Special Orthogonal Group

**变换矩阵**
3维旋转矩阵的集合可以定义为：
$$
\mathrm{SE}(3)=\left \{ \boldsymbol{T}=
\left [ \begin{matrix}
 \boldsymbol{R} & \boldsymbol{t} \\
 \boldsymbol{0}^{\mathrm{T}} & 1
 \end{matrix} \right ]
\in \mathbb{R}^{4 \times 4}
|  \boldsymbol{R}\in \mathrm{SO}(3),
\boldsymbol{t}\in \mathbb{R}^3 \right \} 
$$
$$
\boldsymbol{T}^{-1}=
\left [ \begin{matrix}
 \boldsymbol{R}^{\mathrm{T}} &  -\boldsymbol{R}^{\mathrm{T}}\boldsymbol{t} \\
 \boldsymbol{0}^{\mathrm{T}} & 1
 \end{matrix} \right ]
$$
SE(n)意为特殊欧氏群Special Euclidean Group

**旋转向量**
变换矩阵T描述一个6自由度的三维刚体运动
1. SO(3)的旋转矩阵9个量，但一次旋转只有3个自由度$\rightarrow$冗余
2. SE(3)的变换矩阵16个量表达了6自由度的变换$\rightarrow$冗余

考虑到旋转矩阵自身的约束会给求解带来困难，考虑使用旋转向量刻画旋转
旋转向量又称轴角(Axis-Angle)是一个3维向量，其方向与旋转轴一致，其长度等于旋转角
由罗德里格斯公式(Rodrigues's Formula)，旋转矩阵R与轴角(n,$\theta$)之间满足
$$
\boldsymbol{R}=
\cos{\theta}\boldsymbol{I}+
(1-\cos{\theta})\boldsymbol{n}\boldsymbol{n}^{\mathrm{T}}+
\sin{\theta}\boldsymbol{n}^{\wedge}
$$

$$
\begin{cases}
\theta=
\arccos \frac{\mathrm{tr}(\boldsymbol{R})-1}{2} \\
\boldsymbol{Rn} = \boldsymbol{n}
\end{cases}
$$
轴角的优点：紧凑
缺点：1 不好表达两次连续的旋转 2 旋转矩阵到轴角表示法的映射关系是一对多的关系，存在奇异性问题

**欧拉角**
优点：3个分离的转角，直观且紧凑
缺点：万向锁问题（奇异性问题）
万向锁问题简单来说就是ZYX顺规下，当俯仰角为$\pm 90^{\circ}$时，第3次旋转与第1次旋转效果相同，即1、3次旋转可以用1旋转等效，这样就相当于丢失了1个自由度，这样欧拉角来描述旋转就失效了，因此欧拉角不适合做插值和迭代

理论上可以证明：只要想用3个实数表达三维旋转，都会不可避免地遇到奇异性问题，欧拉角是这样，旋转向量也是这样。换句话说，不存在不带有奇异性的三维向量描述方式。

**四元数** Quaternion
欧拉公式
$$
\rm e^{i\theta} = \cos{\theta}+i\sin{\theta}
$$
单位复数可以描述复平面上向量的旋转，三维旋转可以用**单位四元数**描述
一个四元数有1个实部3个虚部
$$
q=a+bi+cj+dk=[s,\boldsymbol{v}]
$$
其中，$\rm i^2=j^2=k^2=ijk=-1$
四元数乘法不遵守交换律
**由四元数求旋转矩阵**
$$
\boldsymbol{R}=
\boldsymbol{v}\boldsymbol{v}^{\mathrm{T}}+
s^2\boldsymbol{I}+
2s\boldsymbol{v}^{\wedge}+
(\boldsymbol{v}^{\wedge})^2
$$
**由四元数求旋转向量**
$$
\begin{cases}
\theta=2\arccos{s} \\
\boldsymbol{n}=\boldsymbol{v}/\sin{\frac{\theta}{2}}
\end{cases}
$$
由旋转向量求四元数
$$
\begin{cases}
s=\cos{\frac{\theta}{2}} \\
\boldsymbol{v}=\boldsymbol{n}\sin{\frac{\theta}{2}}
\end{cases}
$$
Eigen库的使用


### 3. IMU观测模型


### 4. 李群与李代数
群Group是**一种集合**加上**一种运算**的代数结构
李群Lie Group是指具有连续性质的群，如SO(n)、SE(n)
李代数 $\mathfrak{so}(3)$
每个李群都有与之对应的李代数，李代数描述了李群的局部性质，准确地说，是单位元附近的正切空间
李代数 $\mathfrak{so}(3)$是一个由三维向量组成的集合，每个向量对应一个反对称矩阵，可以用于表达旋转矩阵的导数。它与SO(3)的关系由指数映射给定：
$$
\boldsymbol{R}=e^{\phi^{\wedge}}
$$
结论：$\mathfrak{so}(3)$实际上就是有所谓的旋转向量组成的空间，而指数映射即罗德里格斯公式

## IMU 预积分理论
`zig 2023-11-18`
### 0 PRELIMINARIES
#### 0.1 李群 Lie group
李群（Lie group）是一个数学概念，指具有群结构的光滑微分流形，其群作用与微分结构相容。李群的名字源于挪威数学家索菲斯·李（Marius Sophus Lie）的姓氏，以其为连续变换群奠定基础。
粗略地说，李群是**连续**的群，也即其元素可由几个实参数描述。因此，李群为连续对称性的概念提供了一个自然的模型，例如三维旋转对称性。
群（Group）是一种集合加上一种运算的代数结构。容易验证，旋转矩阵集合和矩阵乘法构成群，同样变换矩阵集合和矩阵乘法也构成群，也就是指常用的特殊正交群SO(n)和特殊欧式群SE(n)。它们在实数空间上是连续的，所以也都是李群。
#### 0.2 由旋转矩阵引出李代数

#### 0.3 李代数 Lie algebra
数学上，李代数是一个代数结构，主要用于研究像李群和微分流形之类的几何对象。李代数因研究无穷小变换的概念而引入。

每个李群都有与之对应的李代数。李代数描述了李群的局部性质，准确地说，是单位元附近的正切空间（tangent space）。

李代数由一个集合、一个数域和一个二元运算（又称为李括号）组成。三维向量 $\mathbb{R}^{3}$ 上定义的叉乘是一种李括号，因此 $ \mathfrak{g}=(\mathbb{R}^{3},\mathbb{R},\times) $ 构成了一个李代数。

李群SO(3)对应的李代数是定义在 $\mathbb{R}^{3}$ 上的向量，在此定义下，两个向量 $ \boldsymbol{\phi}_{1}, \boldsymbol{\phi}_{2}$ 的李括号为：
$$
[\boldsymbol{\phi}_{1}, \boldsymbol{\phi}_{2}] =
(\boldsymbol{\phi}_{1}^{\wedge}\boldsymbol{\phi}_{2}^{\wedge}-
\boldsymbol{\phi}_{2}^{\wedge}\boldsymbol{\phi}_{1}^{\wedge}
)^{\vee}
$$
因此有如下表示：
$$
\mathrm{SO}(3)=\{ \boldsymbol{R}\in \mathbb{R}^{3 \times 3}
| \boldsymbol{RR}^{\mathrm T}=\boldsymbol{I},
\mathrm{det}(\boldsymbol{R})=1 \}
$$

$$
\mathfrak{so}(3)=\{
    \boldsymbol{\phi}\in \mathbb{R}^{3},\boldsymbol{\phi}^{\wedge}\in\mathbb{R}^{3 \times 3}
\}
$$
李代数 $\mathfrak{so}(3)$ 是一个由三维向量组成的集合，每个向量对应一个反对称矩阵，可以用于表达旋转矩阵的导数。它与SO(3)的关系由指数映射给定：
$$
\boldsymbol{R}=e^{\phi^{\wedge}}
$$

#### 0.4 指数映射 Exponential Map
任何矩阵的指数映射可以写成一个泰勒展开，但是只有在收敛的情况下才会有结果，其结果仍是一个矩阵：

#### 0.5 BCH公式与近似形式

