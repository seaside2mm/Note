---
title:      矩阵的python实现       
date:       2019-05-12              # 时间
author:     BY Seaside                     # 作者
categories:
- 线性代数
toc: true
---

## 前言

本文将简单总结线性代数的基本知识点。同时理论结合实际，使用 Python 来进行实践。如果需要跟着进行编程实践，请先确保下列环境已安装：

- [Python](https://www.python.org/) - 编程实践所使用的语言；
- [Numpy](https://pypi.python.org/pypi/numpy) - Python 的数值计算库。





## 矩阵

Python 的 Numpy 库提供了 ndarray 类用于存储高维数组及普通的数组运算，另外提供 matrix 类用来支持矩阵运算。使用 Python 创建矩阵很简单：

### 创建

两种类型可用来创建矩阵对象。使用`array` 和`matrix`方法。

要把一个 matrix 对象转换为 ndarray 对象，可以直接用 `getA()` 方法。而把 ndarray 对象转成 matrix 对象可以用 `asmatrix()` 方法。

```python
M = np.array([[1,2,3],[4,5,6],[7,8,9],[10,11,12]])
# <type 'numpy.ndarray'>

M = np.matrix([[5,2,7],[1,3,4]])  #or np.matrix('5 2 7;1 3 4')
#<class 'numpy.matrixlib.defmatrix.matrix'>
```

### 基本运算

加

点积实现

`np.ma.dot`  [ref](https://github.com/numpy/numpy/blob/master/numpy/ma/core.py)

矩阵乘法

matmul

