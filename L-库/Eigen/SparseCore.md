

# Data Storage Formats
[[cuda-programming.pdf#page=361]]

##  `SparseMatrix` 

## CCS or CRS
是 Eigen 的稀疏模块的最主要的稀疏矩阵。如果中间没有留未占用的空间，就是压缩模式。对应于 `Compressed Column (or Row) Storage Schemes (CCS or CRS)`。

SparseMatrix 包含了 4 个精简的数组：
* Values: 存储非零元素
* InnerIndices: 存储非零元素的行/列索引.
* OuterStarts: 存储每一列/行第一个非零元素在上面两个数组中的索引。
* InnerNNZs: 存储每一列/行中非零元素的数量。Inner 在列优先矩阵中表示一个列，在行优先矩阵中表示一个行。Outer 表示另一个方向。

![](https://raw.githubusercontent.com/seaside2mm/github-photos/master/images/20220930145024.png )

  
`SparseVector` 是一个特例，只有 `Values` 和 `InnerIndices` 数组。没有压缩和非压缩的区别。

`SparseMatrix` 类和 `SparseVector` 类有 3 个模板参数：元素类型，存储顺序，`（ColMajor(默认)或RowMajor）` 和内索引类型（默认 int）。

对于密集 Matrix 类，构造函数的参数是对象的大小。


## 支持的操作和函数

由于稀疏矩阵的特殊存储格式，它没有密集矩阵那样的灵活性。Eigen 的稀疏矩阵模块仅仅实现了密集矩阵 API 的一个子集。稀疏矩阵支持大多数的 `逐元素` 的一元操作符和二元操作符。

下面使用 sm 表示稀疏矩阵，sv 表示稀疏向量，dm 表示密集矩阵，dv 表示密集向量。

![](https://raw.githubusercontent.com/seaside2mm/github-photos/master/images/20221021170058.png )

![](https://raw.githubusercontent.com/seaside2mm/github-photos/master/images/20221021170209.png)
  

### 转换
任何 SparseMatrix 都可以通过 `SparseMatrix::makeCompressed()` 方法变成稀疏模式。此时，`InnerNNZs` 相对于 `OuterStarts` 而言，就是多余的，因 `为InnerNNZs[j] = OuterStarts[j+1]-OuterStarts[j]`。因此 `SparseMatrix::makeCompressed()` 会释放 InnerNNZ 存储空间。

Eigen 的操作总是返回压缩模式的稀疏矩阵。当向一个 SparseMatrix 插入新元素时，会变成非压缩模式。

