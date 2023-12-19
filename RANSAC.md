## RANSAC算法 
Random sample consensus (RANSAC) is an iterative method to estimate parameters of a mathematical model from a set of observed data that contains outliers, when outliers are to be accorded no influence on the values of the estimates.

论文：Random Sample Consensus: A Paradigm for Model Fitting with Applications to Image Analysis and Automated Cartography
随机采样一致：模型拟合应用于图像分析和自动制图的一个范例

### Introduction
传统的参数估计，例如最小二乘法，会使用全部数据用来优化
缺点：These techniques have no internal mechanisms for detecting and rejecting gross errors。算法内部没有检测和拒绝严重误差的机制
当遇到数据中含有未补偿的严重错误时，通常采用的方法是首先使用全部数据得到模型参数，然后将误差偏大（大于某个预先设定的值）的数据视为严重错误数据，将其删除，再进行拟合，不断迭代，直至不再有异常数据出现。事实上，这种方法也并不总是有效的。
因此，处理未经验证的数据，"平均"并不是好方法。
RANSAC算法可以处理包含大量严重错误的数据，可应用于场景分析

### Random Sample Consensus
RANSAC uses as small an initial data set as feasible and enlarges this set with consistent data when possible.
RANSAC算法使用尽可能小的初始数据集，并在可能的情况下使用一致的数据扩展该集
一个简单的例子：使用二维点集拟合一段圆弧，RANSAC算法会选择3个点，计算圆心和半径，然后对与该圆弧足够近的点进行计数。如果这个数量足够，RANSAC算法将使用平滑的方法进一步进行参数估计。如果数量不够，则再随机选3个点，重复这个过程。