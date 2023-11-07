## 非线性最小二乘问题完全攻略
`by zig 2023.10.17`

### 0 基本概念
#### 0.1 函数
**一元函数**：$\mathbf{f}:\mathbb{R} \to \mathbb{R}$ 
**多元函数**：$\mathbf{f}:\mathbb{R}^{n} \to \mathbb{R}$
**向量函数**：$\mathbf{f}:\mathbb{R}^{n} \to \mathbb{R}^{m}$

#### 0.3 梯度
梯度针对多元函数 $\mathbf{f}:\mathbb{R}^{n} \to \mathbb{R}$，是导数的推广，它的结果是一个向量：
$$
\nabla{f}=
\left [ 
\frac{\partial f}{\partial x_1} \ \dots \ 
\frac{\partial f}{\partial x_n}
\right ]^{T}
$$
#### 0.2 最小二乘问题
$$
\min_{\boldsymbol{x}}{F(\boldsymbol{x})}=
\frac{1}{2}
\left \| f(\boldsymbol{x}) \right \| _{2}^{2}   
$$

$f$是任意非线性标量函数 $\mathbf{f}:\mathbb{R}^{n} \to \mathbb{R}$
### 1 梯度下降法与牛顿法
#### 1.1 雅可比矩阵
雅可比矩阵(Jacobian matrix)是函数的一阶偏导数以一定方式排列成的矩阵。当其为方形矩阵时，其行列式称为雅可比行列式。
其重要性在于，如果函数 $\mathbf{f}:\mathbb{R}^{n} \to \mathbb{R}^{m}$ 在点 $ \mathbf{x} $ 可微的话，在点 $ \mathbf{x} $ 的雅可比矩阵即为该函数在该点的最佳线性逼近，也代表雅可比矩阵是单变数实数函数的微分在向量值多变数函数的推广，在这种情况下，雅可比矩阵也被称作函数 $ \mathbf{f} $ 在 $ \mathbf{x} $ 的微分或导数。
$$
\mathbf{J}=
\left [ 
\frac{\partial \mathbf{f}}{\partial x_1} \ \dots \ 
\frac{\partial \mathbf{f}}{\partial x_n}
\right ] 
$$

#### 1.2 海塞矩阵
海塞矩阵(Hessian matrix)是一个由多变量实值函数的所有二阶偏导数组成的方阵。
对于函数 $\mathbf{f}:\mathbb{R}^{n} \to \mathbb{R}$，其Hessian矩阵可记为：
$$
\mathbf{H}_{ij}=
\frac{\partial^{2}{f}}{\partial{x_i} \partial{x_j}}
$$
若函数有n次连续性，则函数的Hessian矩阵是对称矩阵。
函数 $f$ 的Hessian矩阵和Jacobian矩阵之间满足：
$$
\mathbf{H}(f)=\mathbf{J}(\nabla f)^T
$$
#### 1.3 泰勒展开
梯度法旨在寻找使目标函数 $F(\boldsymbol{x})$ 不断下降的增量，经过迭代，得到 $\boldsymbol{x}$ 的最优值。
现在考虑第 $k$ 次迭代，假设我们在 $\boldsymbol{x}_k$ 处，想要寻找此处的增量 $\Delta{\boldsymbol{x}}_k$ ,将目标函数在此处进行泰勒展开：
$$
F(\boldsymbol{x})\approx 
F(\boldsymbol{x}_k)+
\nabla F(\boldsymbol{x}_k) \cdot
(\boldsymbol{x}-\boldsymbol{x}_k)+
\frac{1}{2!} (\boldsymbol{x}-\boldsymbol{x}_k)^T
H(\boldsymbol{x}_k)
(\boldsymbol{x}-\boldsymbol{x}_k)
$$
过渡一步：
$$
F(\boldsymbol{x_k}+\boldsymbol{x}-\boldsymbol{x_k})\approx 
F(\boldsymbol{x}_k)+
J(\boldsymbol{x}_k)
(\boldsymbol{x}-\boldsymbol{x}_k)+
\frac{1}{2!} (\boldsymbol{x}-\boldsymbol{x}_k)^T
H(\boldsymbol{x}_k)
(\boldsymbol{x}-\boldsymbol{x}_k)
$$
最终可得：
$$
F(\boldsymbol{x_k}+\Delta{\boldsymbol{x}}_k)\approx 
F(\boldsymbol{x}_k)+
\color {Red}
J(\boldsymbol{x}_k)
\Delta{\boldsymbol{x}}_k+
\frac{1}{2!}\Delta{\boldsymbol{x}}_k^T
H(\boldsymbol{x}_k)
\Delta{\boldsymbol{x}}_k
$$
如果保留一阶梯度，那么取 $\Delta{\boldsymbol{x}}_k=-J(\boldsymbol{x}_k)$ 即可保证函数下降，在此方向指定一个步长，即可进行迭代计算，这就是**梯度下降法**。
如果保留二阶梯度，此时 $\Delta{\boldsymbol{x}}_k$ 的取值没法直观得出，可以构造辅助函数，令：
$$
G(\Delta{\boldsymbol{x}})=
J(\boldsymbol{x}_k)
\Delta{\boldsymbol{x}}+
\frac{1}{2!}\Delta{\boldsymbol{x}}^T
H(\boldsymbol{x}_k)
\Delta{\boldsymbol{x}}
$$
求导并令导数为零：
$$
\frac{\mathrm{d}G(\Delta{\boldsymbol{x}})}{\mathrm{d}\Delta{\boldsymbol{x}}}=
J(\boldsymbol{x}_k)+\frac{1}{2}[H(\boldsymbol{x}_k)+H(\boldsymbol{x}_k)^T]\Delta{\boldsymbol{x}}
$$
假设 $F(\boldsymbol{x})$ 满足n次连续，则 $ H(\boldsymbol{x}_k)=H(\boldsymbol{x}_k)^T $,
$$
J(\boldsymbol{x}_k)+H(\boldsymbol{x}_k)\Delta{\boldsymbol{x}}=
0 \Rightarrow \mathbf{H}\Delta{\boldsymbol{x}}=-\mathbf{J}
$$
解线性方程可以得到增量 $\Delta{\boldsymbol{x}}_k$ ,这就是**牛顿法**。

### 2 高斯牛顿法
上面的牛顿法和梯度下降法都是针对目标函数 $F(\boldsymbol{x})$ 来进行求解的，在使用牛顿法时不可避免的需要求Hassian矩阵，当问题规模较大时，这个求解会非常困难。我们倾向于避免Hassian的计算，为此，我们选取误差函数 $f(\boldsymbol{x})$ 进行优化求解。
现在考虑第 $k$ 次迭代，假设我们在 $\boldsymbol{x}_k$ 处，想要寻找此处的增量 $\Delta{\boldsymbol{x}}_k$ ，将误差函数在此处进行泰勒展开：
$$
f(\boldsymbol{x})=
f(\boldsymbol{x}_k+\Delta \boldsymbol{x}_k)\approx 
f(\boldsymbol{x}_k)+
J(\boldsymbol{x}_k)
\Delta \boldsymbol{x}_k
$$
我们最终的目标是使得 $F(\boldsymbol{x})$ 最小，也即 $\left \| f(\boldsymbol{x}) \right \| _{2}^{2} $ 最小，此时我构造新的目标函数：
$$
T(\Delta{\boldsymbol{x}})=
\left \| 
f(\boldsymbol{x}_k)+
J(\boldsymbol{x}_k)
\Delta{\boldsymbol{x}}
\right \|_{2}^{2}
$$
可以直观地看到，当 $J(\boldsymbol{x}_k)\Delta{\boldsymbol{x}}$ 越接近 $-f(\boldsymbol{x}_k)$ 时，新的目标函数越小。
因此，要求的增量 $\Delta{\boldsymbol{x}}_k$ 应满足：
$$
J(\boldsymbol{x}_k)\Delta{\boldsymbol{x}}=
-f(\boldsymbol{x}_k)
$$
稍作变形，
$$
J(\boldsymbol{x}_k)^T
J(\boldsymbol{x}_k)\Delta{\boldsymbol{x}}=
-J(\boldsymbol{x}_k)^Tf(\boldsymbol{x}_k)
$$
这个方程是关于增量的线性方程组，通过求解得到增量进行迭代，这就是**牛顿高斯法**。

### 3 列文伯格-马夸尔特方法
列文伯格-马夸尔特方法（Levenberg–Marquardt algorithm）能提供数非线性最小化（局部最小）的数值解。此算法能借由执行时修改参数达到结合高斯-牛顿算法以及梯度下降法的优点，并对两者之不足作改善（比如高斯牛顿法逆矩阵不存在或是初始值离局部极小值太远）。
> The LM algorithm is first shown to be a blend of vanilla gradient descent and Gauss-Newton iteration.

在高斯牛顿法中，我们实际把原来的目标函数近似为一个新的目标函数，这个近似过程是通过将误差函数进行泰勒展开实现的，但是我们并没有考虑近似效果如何。


### Appendix A: [KKT条件](https://zhuanlan.zhihu.com/p/38163970)
Karush-Kuhn-Tucker (KKT)条件是非线性规划(nonlinear programming)最佳解的必要条件。KKT条件将Lagrange乘数法(Lagrange multipliers)所处理涉及等式的约束优化问题推广至不等式。在实际应用上，KKT条件(方程组)一般不存在代数解，许多优化算法可供数值计算选用。这篇短文从Lagrange乘数法推导KKT条件并举一个简单的例子说明解法。
#### A.1 等式约束优化问题

#### A.2 不等式约束优化问题

### Appendix B: [吉洪诺夫正则化]()
#### B.1 适定问题与不适定问题 [?](https://zh.wikipedia.org/wiki/%E9%81%A9%E5%AE%9A%E6%80%A7%E5%95%8F%E9%A1%8C)
适定问题（Well-posed problem）是指满足下列三个要求的问题：
1. 解必须存在; the problem has a solution
2. 解必须唯一; the solution is unique
3. 解能根据初始条件连续变化，不会发生跳变，即解必须稳定。the solution's behavior changes continuously with the initial conditions

上述3个条件，只要一个不满足，则称之为不适定问题（ill-posed problems）
If the problem is well-posed, then it stands a good chance of solution on a computer using a stable algorithm. If it is not well-posed, it needs to be re-formulated for numerical treatment. Typically this involves including additional assumptions, such as smoothness of solution. This process is known as regularization. Tikhonov regularization is one of the most commonly used for regularization of linear ill-posed problems.

#### B.2 超定方程组的最小二乘解
超定方程组：
$$
A_{m \times n}x_{n \times 1} = b_{m \times 1},m>n
$$
超定方程是一组矛盾方程，一般不存在解。但可以通过最小二乘的方法求一个最接近的解，具体过程如下：
构造目标函数 $F(x)=(Ax-b)^T(Ax-b)$ ，展开：
$$
F(x)=
x^TA^TAx-x^TA^Tb-b^TAx+b^Tb
$$
求导：
$$
F^{\prime}(x)=[A^TA+(A^TA)^T]x-A^Tb-(b^TA)^T
$$
整理：
$$
F^{\prime}(x)=2A^TAx-2A^Tb
$$
令 $F^{\prime}(x)=0$ ，求得 $x=(A^TA)^{-1}A^Tb $

这里用到了几个矩阵求导的小技巧：
$$
\frac{\mathrm{d} (x^THx)}{\mathrm{d} x}=(H+H^T)x 
$$
$$
\frac{\mathrm{d} (x^TH)}{\mathrm{d} x}=H
$$
$$
\frac{\mathrm{d} (Hx)}{\mathrm{d} x}=H^T 
$$
#### B.3 吉洪诺夫正则化
当求解超定问题时，矩阵 $A$ 的协方差矩阵 $A^{H}A$ 奇异或接近奇异时，利用最小二乘方法求出的结果 $\hat{x}_{LS}=(A^{H}A)^{-1}A^{H}b$ 会出现发散或对 $x$ 不合理的逼近。
为了解决这一问题，吉洪诺夫于1963年提出了利用正则化项修改最小二乘的代价函数的方法，修改后的代价函数如下：
$$
J(x)=
\frac{1}{2}
(
    \left \|
        Ax-b
    \right \|^{2}_{2}+
    \lambda
    \left \|
        x
    \right \|^{2}_{2}
)
$$
式中 $\lambda \ge 0$ 称为正则化参数，这种方法被称为**吉洪诺夫正则化（Tikhonov Regularization）**。
吉洪诺夫正则化为非适定性问题的正则化中最常见的方法。
在统计学中，本方法被称为脊回归或岭回归（ridge regression）
在机器学习领域则称为权重衰减或权值衰减（weight decay）

使用吉洪诺夫正则化处理B.2中的超定方程组，其过程如下：
构造目标函数 $F(x)=(Ax-b)^T(Ax-b)$ ，展开：
$$
F(x)=
x^TA^TAx-x^TA^Tb-b^TAx+b^Tb + \color {Red} \lambda x^Tx
$$
求导：

$$
F^{\prime}(x)=2A^TAx-2A^Tb+\color {Red} 2\lambda Ix
$$
令 $F^{\prime}(x)=0$ ，求得 $x=(A^TA+{\color {Red} \lambda I})^{-1}A^Tb $
