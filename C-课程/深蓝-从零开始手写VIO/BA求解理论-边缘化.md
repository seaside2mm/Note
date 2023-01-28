
# 协方差和信息矩阵

![[第4节 滑动窗口理论.pdf]]

## Schur complement
<p align="center">
<img src=" https://raw.githubusercontent.com/seaside2mm/github-photos/master/images/20221023183913.png"  width="60%"/>
<img src=" https://raw.githubusercontent.com/seaside2mm/github-photos/master/images/20221021131342.png"  width="40%"/>
<img src=" https://raw.githubusercontent.com/seaside2mm/github-photos/master/images/20221021131538.png"  width="48%"/>
<img src=" https://raw.githubusercontent.com/seaside2mm/github-photos/master/images/20221022153759.png"  width="100%"/>
</p>

# 问题
- 重投影误差最小化
[](https://proc-cpuinfo.fixstars.com/wp-content/uploads/2020/10/ba-3.png)
- 边缘化
SLAM 中有一个加速增量方程求解的方法，称为边缘化。边缘化是说，如果我们把待优化的相机位姿放在 H 矩阵的左上角，把待优化的路标点放在 H 矩阵的右下角，再把 H 矩阵分为四块，就可以对 H 的矩阵块进行高斯消元，使得对相机位姿的求解不依赖于路标点。这种方法奏效的原因是因为相机矩阵相比于路标点稀疏得多，因此相机矩阵块求逆更容易。当然，具体的分析建议阅读高翔的《视觉 SLAM 十四讲》。
- 信息矩阵
$H=J^TWJ,\\b=JWe$，
> `J` 是误差对位姿等的雅克比，`W` 是权重。一般这个 `H` 矩阵也称为信息矩阵，并且 H 矩阵是稀疏的.
信息矩阵和信息向量，其实是另一组描述高斯分布的参数，叫做 `canonical parameterization`.
在 GraphSLAM 中，我们需要通过优化信息矩阵来求得合理的 pose 和 map
总结：
信息矩阵等于协方差的逆，在图优化中作为不确定性的度量。这个矩阵主要是在构成最小二乘优化问题的时候用到的，其实理解成权重更好，协方差越大，说明距离真值的误差越大，那么在优化的时候就要给予较小的权重，不至于误差较大的顶点或者边带偏了整个优化。
## 鲁棒核函数
g2o 实现了一个 RobustKernel 的虚基类。这个虚基类提供了设置和获得核函数参数 lambda 的接口和一个 robustify 虚函数，用来计算鲁棒核函数的值、一阶导数和二阶导数，存储在一个 Vector3D 变量 rho 中。
鲁棒核函数的真正实现在 robust_kernel_impl 文件中。g2o 实现了多个鲁棒核函数，常见的 Huber 核、Cauchy 核等都有。
[20221020100238](https://raw.githubusercontent.com/seaside2mm/github-photos/master/images/20221020100238.png)