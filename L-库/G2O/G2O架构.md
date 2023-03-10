  
  

<p align="center">

  <img src="https://raw.githubusercontent.com/seaside2mm/github-photos/master/images/20221007161203.png"  width="120%"/>

</p>

  
  

1. 左上角`HyperGraph`提供了顶点和边构成的拓扑图，它只关注图的**连接关系**，不负责优化相关的工作。

2. 其派生类`OptimizableGraph`为顶点和边**提供了可优化的功能**。

3. 再往下是`OptimizableGraph`的派生类`SparseOptimizer`，显然，对于构建的图优化问题，SparseOptimizer提供了**稀疏求解的方案**，这也是SLAM能够达到实时性所依赖的关键技术。以上，是优化器相关的部分。

  
  

4. 再往下，优化器*包含*了一个`OptimizationAlgorithm`，这是**优化算法的基类**，优化器会调用该优化算法实现优化。右边给出了优化算法的具体实现，`OptimizationWithHessian`包含了一系列基于Hessian矩阵求解增量方程的优化算法，包括`OptimizationAlgorithmGaussNewton`、`OptimizationAlgorithmLevenberg`和`OptimizationAlgorithmDogleg`。不同的优化算法给出了不同的梯度下降策略，也就是说，用**不同的方式找出增量的方向和大小**，但迭代求解的本质是一样的。

  

5. 再往右，优化算法*包含*一个`Solver`求解器。不论使用了什么优化算法，每次迭代都需要求解一个 $H∆x=g$ 的增量方程，其中H是Hessian矩阵或其变体，$∆x$是待求的优化变量的**更新量**。那么如何求解这个方程，就是Solver的工作了。

  

6. 在g2o中，只提供了==一个实现类==`BlockSolver<>`。所谓块求解器，就是利用`A`矩阵的稀疏性，每个优化变量和误差项都体现为固定大小的矩阵块，可以利用它的一些性质加速计算。在块求解器中，*包含*了一个`SparseBlockMatrix<T>`和`LinearSolver`，其实前者用来存放**H矩阵**的数据，后者用来指定具体的线性求解器。

7. 之所以叫线性求解器，是因为增量方程是一个线性方程。g2o提供的线性求解器有三个，分别是`LinearSolverCSparse<>、LinearSolverCholmod<>和LinearSolverPCG<>`。它们之间的不同大概只是对*矩阵求逆*的方式不同，可能会有速度上的差异，但结果一定一致。

  

8. 最后，右上角的一大部分是顶点和边，这些比较容易理解，也是我们编程中接触得最多的，这里不再详述。

  
  

# 参考

[ORB-SLAM2 代码解读（三）：优化 2（详解 + g2o 使用）](https://wym.netlify.app/2019-07-05-orb-slam2-optimization2/)

[g2o学习笔记](https://www.jianshu.com/p/e16ffb5b265d)

[g2o论文理解](https://blog.csdn.net/YuYunTan/article/details/85255345)

[api文档](http://docs.ros.org/en/fuerte/api/re_vision/html/namespaceg2o.html)

  

https://zhuanlan.zhihu.com/p/100522179