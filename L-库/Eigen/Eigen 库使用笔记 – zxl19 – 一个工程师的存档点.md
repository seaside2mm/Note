---
url: https://zxl19.github.io/eigen-note/
title: Eigen 库使用笔记 – zxl19 – 一个工程师的存档点
date: 2023-01-20 17:14:26
tag: 
summary: 我的 Eigen 库使用笔记。
---
我的 Eigen 库使用笔记。

## Eigen Hello World

1.  Eigen 是纯头文件库，定义的类、函数、常量等均在`Eigen`命名空间中，为了使表示形式简便，本文默认使用`Eigen`命名空间：
    
2.  Eigen 稠密矩阵和数组模块功能可以查询 [Dense matrix and array manipulations](https://eigen.tuxfamily.org/dox/group__QuickRefPage.html)；
3.  Eigen 稀疏线性代数模块功能可以查询 [Sparse linear algebra](https://eigen.tuxfamily.org/dox/group__SparseQuickRefPage.html)；
4.  `#include <Eigen/Eigen>`（全部模块功能）=`#include <Eigen/Dense>`（绝大部分模块功能）+`#include <Eigen/Sparse>`（稀疏矩阵模块功能）；
5.  `#include <Eigen/Core>`为核心模块，包含`Matrix`类和`Array`类的定义、基础的线性代数操作等；
6.  `#include <Eigen/Geometry>`为几何模块，包含 SLAM 相关的位姿表示、四元数、旋转向量等；
7.  对于 MATLAB 用户，可以参考 [Eigen short ASCII reference](https://eigen.tuxfamily.org/dox/AsciiQuickReference.txt) 快速入门，[zxl19/Eigen-Cheatsheet](https://github.com/zxl19/Eigen-Cheatsheet) 将其整理成 Markdown 和 PDF 文档；
8.  `Matrix` 模板类定义了矩阵和向量，用于进行线性代数运算，`Array` 模板类定义了数组，用于进行类似 MATLAB 的逐元素操作，以 `Matrix` 模板类的原型声明为例对于模板参数进行说明： 

```cpp
Matrix<typename Scalar,                          // [必需] 数据类型
int RowsAtCompileTime,                           // [必需] 编译时确定的行数，如果不确定可以使用Dynamic指定动态大小
int ColsAtCompileTime,                           // [必需] 编译时确定的列数，如果不确定可以使用Dynamic指定动态大小
int Options = 0,                                 // [可选] 位字段，可以使用RowMajor指定逐行保存，或者使用ColMajor指定逐列保存，默认逐列保存
int MaxRowsAtCompileTime = RowsAtCompileTime,    // [可选] 编译时确定的最大行数，用于避免动态内存分配
int MaxColsAtCompileTime = ColsAtCompileTime>    // [可选] 编译时确定的最大列数，用于避免动态内存分配
```

9. Eigen 使用 `typedef` 关键字重命名了常用大小的矩阵和数组，后缀 `f` 代表 `float`、`d` 代表 `double`、`i` 代表 `int`，不同数据类型的矩阵在运算中禁止混用，需要显式类型转换，在使用中应尽可能少用动态大小的矩阵和数组，以提高运行速度： 

```cpp
 // Matrix类
 Matrix<float, Dynamic, Dynamic>     <=>     MatrixXf
 Matrix<double, Dynamic, 1>          <=>     VectorXd
 Matrix<int, 1, Dynamic>             <=>     RowVectorXi
 Matrix<float, 3 , 3>                <=>     Matrix3f
 Matrix<float, 4, 1>                 <=>     Vector4f
 // Array类
 Array<float, Dynamic, Dynamic>      <=>     ArrayXXf
 Array<double, Dynamic, 1>           <=>     ArrayXd
 Array<int, 1, Dynamic>              <=>     RowArrayXi
 Array<float, 3, 3>                  <=>     Array33f
 Array<float, 4, 1>                  <=>     Array4f
```
1.  `Matrix`类和`Array`类之间运算和类型转换的规则：
    
```cpp
Array44f a1, a2;
Matrix4f m1, m2;
m1 = a1 * a2;                       // 逐元素相乘，运算结果从Array类到Matrix类隐式类型转换
a1 = m1 * m2;                       // 矩阵相乘，运算结果从Matrix类到Array类隐式类型转换
a2 = a1 + m1.array();               // Array类和Matrix类的对象在运算中禁止混用，需要显式类型转换
m2 = a1.matrix() + m1;              // Array类和Matrix类的对象在运算中禁止混用，需要显式类型转换
ArrayWrapper<Matrix4f> m1a(m1);     // m1a相当于m1.array()，二者参数相同
MatrixWrapper<Array44f> a1m(a1);    // a1m相当于a1.matrix()，二者参数相同
```
    
11.  `Matrix`类和`Array`类的初始化方式，建议在定义后对变量进行初始化；
    
    *   固定大小——大小确定，可不加行列数：
        
```cpp
typedef {Matrix3f | Array33f} FixedXD;
FixedXD x;
// 创建对应类的对象（作为等号右值）
x = FixedXD::Identity();
x = FixedXD::Zero();
x = FixedXD::Ones();
x = FixedXD::Constant(value);
x = FixedXD::Random();
x = FixedXD::LinSpaced(size, low, high);
// 调用对应成员函数
x.setIdentity();
x.setZero();
x.setOnes();
x.setConstant(value);
x.setRandom();
x.setLinSpaced(size, low, high);

```
        
    *   二维动态大小：
        
        ```
          typedef {MatrixXf | ArrayXXf} Dynamic2D;
          Dynamic2D x;
          // 创建对应类的对象（作为等号右值）
          x = Dynamic2D::Identity(rows, cols);
          x = Dynamic2D::Zero(rows, cols);
          x = Dynamic2D::Ones(rows, cols);
          x = Dynamic2D::Constant(rows, cols, value);
          x = Dynamic2D::Random(rows, cols);
          // 调用对应成员函数
          x.setIdentity(rows, cols);
          x.setZero(rows, cols);
          x.setOnes(rows, cols);
          x.setConstant(rows, cols, value);
          x.setRandom(rows, cols);
        
        
        ```
        
    *   一维动态大小：
        
        ```
          typedef {VectorXf | ArrayXf} Dynamic1D;
          Dynamic1D x;
          // 创建对应类的对象（作为等号右值）
          x = Dynamic1D::Zero(size);
          x = Dynamic1D::Ones(size);
          x = Dynamic1D::Constant(size, value);
          x = Dynamic1D::Random(size);
          x = Dynamic1D::LinSpaced(size, low, high);
          // 调用对应成员函数
          x.setZero(size);
          x.setOnes(size);
          x.setConstant(size, value);
          x.setRandom(size);
          x.setLinSpaced(size, low, high);
        
        
        ```
        

## Matrix 类

### 矩阵初始化

#### 初始化为特殊矩阵

```
// 基向量
x = Vector3f::UnitX(); // 1 0 0
x = Vector3f::UnitY(); // 0 1 0
x = Vector3f::UnitZ(); // 0 0 1
x = Vector4f::Unit(i);
x.setUnit(i);
x = VectorXf::Unit(size, i);
x.setUnit(size, i);


```

#### 逐元素初始化

可以使用`<<`运算符逐行对于元素进行初始化，也可以使用下标对于特定元素进行初始化：

```
// 二维固定大小
Matrix2d A;
A << 1.0, 0.0,
    0.0, 1.0;
A(0, 0) = 1.0;
A(0, 1) = 0.0;
A(1, 0) = 0.0;
A(1, 1) = 1.0;
// 二维动态大小
int rows = 5, cols = 5;
MatrixXf m(rows, cols);
// .finished()成员函数返回初始化后的临时变量
m << (Matrix3f() << 1, 2, 3, 4, 5, 6, 7, 8, 9).finished(),
     MatrixXf::Zero(3, cols - 3),
     MatrixXf::Zero(rows - 3, 3),
     MatrixXf::Identity(rows - 3, cols - 3);
// 一维固定大小
Vector4d b(0, 1, 2, 3);
b << 0, 1, 2, 3;
b(0) = 0;
b(1) = 1;
b(2) = 2;
b(3) = 3;
// 仅适用于向量
b[0] = 0;
b[1] = 1;
b[2] = 2;
b[3] = 3;
// 仅适用于向量的前四个元素
b.x() = 0;
b.y() = 1;
b.z() = 2;
b.w() = 3;


```

#### 从内存中映射

```
float array[3];
// 映射，array内容随着矩阵内容变化
Vector3f::Map(array).fill(10);
int data[4] = {1, 2, 3, 4};
// 初始化，data内容被复制到矩阵中
Matrix2i mat2x2(data);
// 映射，data内容随着矩阵内容变化
Matrix2i::Map(data) = 2 * mat2x2;
MatrixXi::Map(data, 2, 2) += mat2x2;


```

### 矩阵操作

#### 计算大小

```
// 向量长度
x.size()
// 矩阵行数、列数
C.rows()
C.cols()


```

#### 转置

```
// 以矩阵为例，向量同理
// 转置
C.transpose()
C.transposeInPlace()    // in-place version
// 共轭转置
C.adjoint()
C.adjointInPlace()      // in-place version


```

在使用矩阵的转置给自身赋值时需要避免混淆现象（aliasing）：

```
// 错误，存在混淆现象
C = C.transpose();
C = C.adjoint();
// 正确，避免混淆现象
C = C.transposeInPlace();
C = C.adjointInPlace();


```

#### 求和

#### 取模

```
x.norm()            // 取模
x.squaredNorm()     // 模的平方


```

#### 类型转换

```
// 以矩阵为例，向量同理
C.cast<double>()    // 转为double类型
C.cast<float>()     // 转为float类型
C.cast<int>()       // 转为int类型
C.real()            // 逐元素取实部
C.imag()            // 逐元素取虚部
C.conjugate()       // 逐元素取共轭


```

#### 改变大小

```
// 大小改变后删除原有数据，大小不变时无操作
// 向量
vector.resize(size);
// 矩阵
matrix.resize(nb_rows, nb_cols);
matrix.resize(NoChange, nb_cols);
matrix.resize(nb_rows, NoChange);
// 大小改变后保留原有数据
// 向量
vector.resizeLike(other_vector);
vector.conservativeResize(size);
// 矩阵
matrix.resizeLike(other_matrix);
matrix.conservativeResize(nb_rows, nb_cols);


```

#### 获取运行时信息

```
// 返回指向第一个元素的指针
// 向量
x.data()
// 矩阵
C.data()
// 返回内部步长（inner stride），即相邻元素之间的内存地址增量
// 向量
x.innerStride()
// 矩阵
C.innerStride()
// 返回外部步长（outer stride），即相邻行或列之间的内存地址增量，取决于矩阵是逐行保存还是逐列保存
// 矩阵
C.outerStride()


```

#### 分块

```
// 向量
// Eigen                           // MATLAB
x.head(n)                          // x(1:n)
x.head<n>()                        // x(1:n)
x.tail(n)                          // x(end - n + 1: end)
x.tail<n>()                        // x(end - n + 1: end)
x.segment(i, n)                    // x(i+1 : i+n)
x.segment<n>(i)                    // x(i+1 : i+n)
// 矩阵
// Eigen                           // MATLAB
P.block(i, j, rows, cols)          // P(i+1 : i+rows, j+1 : j+cols)
P.block<rows, cols>(i, j)          // P(i+1 : i+rows, j+1 : j+cols)
P.row(i)                           // P(i+1, :)
P.col(j)                           // P(:, j+1)
P.leftCols<cols>()                 // P(:, 1:cols)
P.leftCols(cols)                   // P(:, 1:cols)
P.middleCols<cols>(j)              // P(:, j+1:j+cols)
P.middleCols(j, cols)              // P(:, j+1:j+cols)
P.rightCols<cols>()                // P(:, end-cols+1:end)
P.rightCols(cols)                  // P(:, end-cols+1:end)
P.topRows<rows>()                  // P(1:rows, :)
P.topRows(rows)                    // P(1:rows, :)
P.middleRows<rows>(i)              // P(i+1:i+rows, :)
P.middleRows(i, rows)              // P(i+1:i+rows, :)
P.bottomRows<rows>()               // P(end-rows+1:end, :)
P.bottomRows(rows)                 // P(end-rows+1:end, :)
P.topLeftCorner(rows, cols)        // P(1:rows, 1:cols)
P.topRightCorner(rows, cols)       // P(1:rows, end-cols+1:end)
P.bottomLeftCorner(rows, cols)     // P(end-rows+1:end, 1:cols)
P.bottomRightCorner(rows, cols)    // P(end-rows+1:end, end-cols+1:end)
P.topLeftCorner<rows,cols>()       // P(1:rows, 1:cols)
P.topRightCorner<rows,cols>()      // P(1:rows, end-cols+1:end)
P.bottomLeftCorner<rows,cols>()    // P(end-rows+1:end, 1:cols)
P.bottomRightCorner<rows,cols>()   // P(end-rows+1:end, end-cols+1:end)


```

### 矩阵运算

#### 方阵相关

```
C.trace()           // 迹
C.inverse()         // 逆
C.determinant()     // 行列式


```

#### 点乘和叉乘

```
// 点乘
x.dot(y)
x.transpose() * y
// 叉乘
x.cross(y)


```

#### 逐元素运算

1.  `Matrix`类可以通过调用对应成员函数实现逐元素运算，此时返回值类型为`Matrix`类：
    
    ```
     mat1.cwiseMin(mat2)                 mat1.cwiseMin(scalar)
     mat1.cwiseMax(mat2)                 mat1.cwiseMax(scalar)
     mat1.cwiseAbs2()
     mat1.cwiseAbs()
     mat1.cwiseSqrt()
     mat1.cwiseInverse()
     mat1.cwiseProduct(mat2)
     mat1.cwiseQuotient(mat2)
     mat1.cwiseEqual(mat2)               mat1.cwiseEqual(scalar)
     mat1.cwiseNotEqual(mat2)
    
    
    ```
    
2.  也可以将`Matrix`类转换为`Array`类后调用对应成员函数实现逐元素运算，此时返回值类型为`Array`类：
    
    ```
     mat1.array().min(mat2.array())      mat1.array().min(scalar)
     mat1.array().max(mat2.array())      mat1.array().max(scalar)
     mat1.array().abs2()
     mat1.array().abs()
     mat1.array().sqrt()
     mat1.array().inverse()
     mat1.array() * mat2.array()
     mat1.array() / mat2.array()
     mat1.array() == mat2.array()        mat1.array() == scalar
     mat1.array() != mat2.array()
    
    
    ```
    

#### 求解 Ax=b 形式线性方程组

Eigen 提供的全部稠密矩阵分解方法及其特性和数值稳定性可以参考 [Catalogue of dense decompositions](https://eigen.tuxfamily.org/dox/group__TopicLinearAlgebraDecompositions.html)。

##### 有解析解

```
x = A.inverse() * b;                    // 直接求逆，速度最慢
x = A.partialPivLu().solve(b);          // 部分主元LU分解，要求可逆阵
x = A.colPivHouseholderQr().solve(b);   // 列主元QR分解
x = A.llt().solve(b);                   // Cholesky分解，要求正定阵
x = A.ldlt().solve(b);                  // LDLT分解，要求正定阵或非负定阵


```

1.  Eigen 提供的全部求解方法及其对于矩阵要求、求解速度、求解精度的对比可以参考 [Linear algebra and decompositions](https://eigen.tuxfamily.org/dox/group__TutorialLinearAlgebra.html)；
2.  Eigen 提供的全部求解方法及其在不同大小矩阵下求解速度的定量对比可以参考 [Benchmark of dense decompositions](https://eigen.tuxfamily.org/dox/group__DenseDecompositionBenchmark.html)；

##### 无解析解

求解最小二乘问题：

1.  Eigen 提供的三种求解方法可以参考 [Solving linear least squares systems](http://www.eigen.tuxfamily.org/dox/group__LeastSquares.html)；
2.  三种求解方法及其求解速度和求解精度的对比：
    
    <table><thead><tr><th>求解方法</th><th>求解速度</th><th>求解精度</th></tr></thead><tbody><tr><td>SVD 分解</td><td>低</td><td>高</td></tr><tr><td>列主元 QR 分解</td><td>中</td><td>中</td></tr><tr><td>求解正规方程</td><td>高</td><td>低</td></tr></tbody></table>

```
x = A.bdcSvd(ComputeThinU | ComputeThinV).solve(b);         // SVD分解
x = A.colPivHouseholderQr().solve(b);                       // 列主元QR分解
x = (A.transpose() * A).ldlt().solve(A.transpose() * b);    // 求解正规方程
relative_error = (A * x - b).norm() / b.norm();             // L2范数相对误差


```

#### 特征值计算

```
Matrix3f A;
// 初始化方式1
EigenSolver<Matrix3f> eigen_solver(A);
// 初始化方式2，可用于计算多个矩阵的特征值
EigenSolver<Matrix3f> eigen_solver;
eigen_solver.compute(A);
if (eigen_solver.info() == Success) {
    // 计算成功
    Vector3f eigen_value = eigen_solver.eigenvalues();      // 特征值
    Matrix3f eigen_vector = eigen_solver.eigenvectors();    // 特征向量
} else if (eigen_solver.info() == NoConvergence) {
    // 计算失败
    std::cout << "Computation Failed!" << std::endl;
}
// 实对称矩阵可以保证对角化成功，初始化方式和查看计算结果方式与上面相同
SelfAdjointEigenSolver<Matrix3f> eigen_solver(A.transpose() * A);


```

#### 奇异值分解

```
// 方阵
Matrix3f A;
// A = USV*
// 参数ComputeFullU和ComputeFullV分别表示计算方阵U和方阵V
// 此时奇异值矩阵S为方阵
JacobiSVD<Matrix3f> svd(A, ComputeFullU | ComputeFullV);
Matrix3f U = svd.matrixU();
Matrix3f V = svd.matrixV();
Vector3f sv = svd.singularValues();
// 非方阵
MatrixXf A;
// A = USV*
// 参数ComputeFullU和ComputeFullV分别表示计算方阵U和方阵V
// 此时奇异值矩阵S为非方阵
JacobiSVD<MatrixXf> svd(A, ComputeFullU | ComputeFullV);
// 参数ComputeThinU和ComputeThinV分别表示计算非方阵U和非方阵V
// 此时奇异值矩阵S为方阵
JacobiSVD<MatrixXf> svd(A, ComputeThinU | ComputeThinV);
MatrixXf U = svd.matrixU();
MatrixXf V = svd.matrixV();
VectorXf sv = svd.singularValues();


```

## Array 类

### 逐元素运算

包括算术运算符、比较运算符、常用数学函数等：

```
array1.abs2()
array1.abs()                  abs(array1)
array1.sqrt()                 sqrt(array1)
array1.log()                  log(array1)
array1.log10()                log10(array1)
array1.exp()                  exp(array1)
array1.pow(array2)            pow(array1,array2)
array1.pow(scalar)            pow(array1,scalar)
                              pow(scalar,array2)
array1.square()
array1.cube()
array1.inverse()

array1.sin()                  sin(array1)
array1.cos()                  cos(array1)
array1.tan()                  tan(array1)
array1.asin()                 asin(array1)
array1.acos()                 acos(array1)
array1.atan()                 atan(array1)
array1.sinh()                 sinh(array1)
array1.cosh()                 cosh(array1)
array1.tanh()                 tanh(array1)
array1.arg()                  arg(array1)

array1.floor()                floor(array1)
array1.ceil()                 ceil(array1)
array1.round()                round(aray1)

array1.isFinite()             isfinite(array1)
array1.isInf()                isinf(array1)
array1.isNaN()                isnan(array1)


```

## 位姿表示

旋转向量、四元数表示的位姿可以直接当做旋转矩阵参与运算，因为重载了乘法运算符。

不同表示方式的位姿在赋值时需要进行显式类型转换。

### 位姿初始化

#### 旋转向量

旋转向量并不是为了储存旋转，而是为了更加方便地创建其他类型的旋转。

旋转轴必须是单位向量，推荐使用基向量。

示例：

```cpp
// 3D旋转矩阵直接使用Matrix3d或Matrix3f
Matrix3d rotation_matrix = Matrix3d::Identity();
// 旋转向量使用AngleAxis，其底层不直接是Matrix类，但运算可以当作矩阵（因为重载了运算符）
AngleAxisd rotation_vector(M_PI / 4, Vector3d(0, 0, 1));    // 沿Z轴旋转45度
rotation_matrix = rotation_vector.matrix();                 // 旋转向量转旋转矩阵
rotation_matrix = rotation_vector.toRotationMatrix();       // 旋转向量转旋转矩阵
// 用AngleAxis进行坐标变换
Vector3d v(1, 0, 0);
Vector3d v_rotated = rotation_vector * v;
// 相当于用旋转矩阵进行坐标变换
Vector3d v_rotated = rotation_matrix * v;
```

#### 欧拉角

示例：

```cpp
// 欧拉角转旋转矩阵，借助旋转向量
AngleAxisd roll_vector(roll_rad, Vector3d::UnitX());
AngleAxisd pitch_vector(pitch_rad, Vector3d::UnitY());
AngleAxisd yaw_vector(yaw_rad, Vector3d::UnitZ());
Matrix3d rotation_matrix = (roll_vector * pitch_vector * yaw_vector).toRotationMatrix();
// 旋转矩阵转欧拉角
Vector3d euler_angles = rotation_matrix.eulerAngles(2, 1, 0);   // ZYX顺序，即yaw-pitch-roll顺序
```

#### 四元数

必须使用单位 #四元数 表示旋转。

示例：

```cpp
// 直接初始化，注意参数顺序为(w，x，y，z)
Quaterniond q = Quaterniond(q_w, q_x, q_y, q_z);
// 可以把AngleAxis赋值给四元数，反之亦然
Quaterniond q = Quaterniond(rotation_vector);
// 可以把旋转矩阵赋值给四元数
Quaterniond q = Quaterniond(rotation_matrix);
// 向量v1和向量v2之间的旋转，将向量v1旋转到与向量v2同向，对于两个向量的模长没有要求
Quaterniond q = Quaterniond::FromTwoVectors(v1, v2);
// 访问四元数中各元素
q.x()
q.y()
q.z()
q.w()
q.coeffs()              // 注意coeffs的顺序是(x, y, z, w)，w为实部，前三者为虚部，这也是Eigen中四元数的存储顺序
q.normalize()           // 归一化四元数，直接对原四元数操作，无返回值，不能作为等号右值
q.normalized()          // 归一化四元数，原四元数不变，返回拷贝构造值，可以作为等号右值
q.matrix()              // 四元数转旋转矩阵
q.toRotationMatrix()    // 四元数转旋转矩阵
// 使用四元数旋转一个向量，使用重载的乘法即可
v_rotated = q * v;      // 注意数学上是qvq^{-1}


```

#### 欧式变换

示例：

```cpp
// 欧氏变换矩阵使用Isometry
Isometry3d T = Isometry3d::Identity();  // 虽然称为3d，实质上是4*4的矩阵
T.rotate(rotation_vector);              // 按照rotation_vector进行旋转
T.pretranslate(Vector3d(1, 2, 3))       // 把平移向量设成(1, 2, 3)
T.matrix()                              // 欧氏变换矩阵
// 用欧氏变换矩阵进行坐标变换
Vector3d v_transformed = T * v;         // 相当于R * v + t
```

### 位姿相关运算

#### 旋转矩阵归一化

1.  四元数法：
    
    通过将旋转矩阵转换为四元数，将四元数归一化后再转回旋转矩阵。
    
    ```cpp
     Matrix3f R;
     Quaternionf q(R);
     R = q.normalized().toRotationMatrix();
    ```
    
2.  SVD 分解法：
    
    ```
     Matrix3f R;
     JacobiSVD<MatrixXf> svd(R, ComputeFullU | ComputeFullV);
     R = svd.matrixU() * svd.matrixV().transpose();
    
    
    ```
    
3.  流形投影法：
    
    ```
     Matrix3f R;
     Matrix3f H = R * R.transpose();
     Matrix3f L = H.llt().matrixL();
     R = L.inverse() * R;
    
    
    ```
    

## Eigen 中的内存对齐问题

### 内存对齐与向量化运算

1.  内存对齐是向量化运算（vectorization）的前提；
2.  向量化运算指的是使用单指令多数据流（Single Instruction Multiple Data，SIMD）指令集，实现一条指令对多个操作数的运算，进而实现运算加速；
3.  常用的 SIMD 指令集包括 SSE（Streaming SIMD Extensions）、AVX（ Advanced Vector Extensions）等，其中 SSE 的操作数要求 16 字节对齐，AVX 的操作数要求 32 字节对齐；

### 固定大小可向量化的 Eigen 对象

1.  Eigen 将编译时大小固定，并且大小是 16 字节的整数倍的对象称为固定大小可向量化的（fixed-size vectorizable），主要包括：
    
    ```
     Vector2d
     Vector4d
     Vector4f
     Matrix2d
     Matrix2f
     Matrix4d
     Matrix4f
     Affine3d
     Affine3f
     Quaterniond
     Quaternionf
    
    
    ```
    
2.  目前向量化运算支持 128 位数据包（例如 SSE、AltiVec）、256 位数据包（例如 AVX）、512 位数据包（例如 AVX512），分别对应 16 字节、32 字节、64 字节，对于这些大小的数据包读写效率最高，因此对于固定大小可向量化的 Eigen 对象进行内存对齐有利于提高运算效率；
    

### 包含 Eigen 对象的类和结构体

1.  对于包含固定大小可向量化的 Eigen 对象的类和结构体，如果需要使用`new`关键字进行动态内存分配，则需要将宏`EIGEN_MAKE_ALIGNED_OPERATOR_NEW`声明为类和结构体的公有成员，这样在动态内存分配时会自动进行内存对齐：
    
    ```
     class Foo {
      private:
       Vector4d v;
    
      public:
       EIGEN_MAKE_ALIGNED_OPERATOR_NEW
     };
    
     Foo* foo = new Foo;
    
    
    ```
    
    *   如果使用 C++17 标准进行编译则不需要上述操作，因为 C++17 标准有对于超出默认对齐尺寸（over-aligned）数据的动态内存分配机制；
    *   在 C++17 标准下，宏`EIGEN_MAKE_ALIGNED_OPERATOR_NEW`定义为空；
    *   尽量将类和结构体中的 Eigen 对象声明一起放在类和结构体的开头，这并不是 Eigen 内存对齐的强制要求，但是可以节省内存对齐的空间；
    *   动态大小的 Eigen 对象自动进行动态内存分配，自动进行内存对齐，不需要上述操作；
2.  如果需要根据模板参数进行条件判断是否使用内存对齐，则需要使用宏`EIGEN_MAKE_ALIGNED_OPERATOR_NEW_IF`进行判断：
    
    ```
     template<int n>
     class Foo {
       typedef Matrix<float, n, 1> Vector;
       enum { NeedsToAlign = (sizeof(Vector) % 16) == 0 };
       Vector v;
      public:
       EIGEN_MAKE_ALIGNED_OPERATOR_NEW_IF(NeedsToAlign)
     };
    
     Foo<4> *foo4 = new Foo<4>; // foo4 is guaranteed to be 128bit-aligned
     Foo<3> *foo3 = new Foo<3>; // foo3 has only the system default alignment guarantee
    
    
    ```
    
    *   在参数`NeedsToAlign`为真时，自动添加宏`EIGEN_MAKE_ALIGNED_OPERATOR_NEW`进行内存对齐；
    *   如果使用 C++17 标准进行编译则不需要上述操作，因为 C++17 标准有对于超出默认对齐尺寸数据的动态内存分配机制；
    *   在 C++17 标准下，宏`EIGEN_MAKE_ALIGNED_OPERATOR_NEW_IF`定义为空；
3.  如果不想在多处声明宏`EIGEN_MAKE_ALIGNED_OPERATOR_NEW`，可以通过以下两个方法：
    
    *   使用`DontAlign`关闭内存对齐，但是这会带来加载和存储上的额外耗时，具体影响程度主要取决于硬件：
        
        ```cpp class Foo { private: Matrix<double, 4, 1, DontAlign> v; }
        
    *   将固定大小可向量化的 Eigen 对象指针声明为类和结构体的私有成员，在创建类和结构体的对象时动态分配内存，优点是类和结构体不受到内存对齐的影响，缺点是需要额外的堆（heap）分配：
        
        ```
          struct Foo_d {
            EIGEN_MAKE_ALIGNED_OPERATOR_NEW
            Vector4d v;
          };
        
          struct Foo {
            Foo() { init_d(); }
            ~Foo() { delete d; }
            void bar() {
              // use d->v instead of v
            }
           private:
            void init_d() { d = new Foo_d; }
            Foo_d* d;
          };
        
        
        ```
        

### 包含 Eigen 对象的 STL 容器

1.  对于包含固定大小可向量化的 Eigen 对象的 STL 容器，需要使用 Eigen 定义的超出默认对齐尺寸的堆内存管理器（over-aligned allocator）`aligned_allocator`进行内存对齐：
    
    ```
     std::map<int, Vector4d, std::less<int>, aligned_allocator<std::pair<const int, Vector4d> > >
    
    
    ```
    
    *   `std::less`是`std::map`默认的排序函数，但是在这里为了指定堆内存管理器类型需要在其之前指定；
    *   如果使用 C++17 标准进行编译则不需要上述操作，因为 C++17 标准有对于超出默认对齐尺寸数据的动态内存分配机制；
2.  如果需要使用`std::vector`，除了需要使用 Eigen 定义的堆内存管理器`aligned_allocator`，还需要包含头文件`#include <Eigen/StdVector>`进行内存对齐：
    
    ```
     #include <Eigen/StdVector>
    
     std::vector<Vector4f, aligned_allocator<Vector4f> >
    
    
    ```
    
    *   C++11 之前的标准在调用`std::vector`的`resize()`成员函数时会调用元素的默认构造函数，造成元素的值传递，会破坏内存对齐；
    *   如果使用 C++11 以后的标准进行编译则不需要上述操作，因为 C++11 标准修复了之前标准中`std::vector`存在的问题；
3.  如果需要根据模板参数进行条件判断`std::vector`是否使用内存对齐，则需要使用宏`EIGEN_DEFINE_STL_VECTOR_SPECIALIZATION`进行判断：
    
    ```
     #include<Eigen/StdVector>
    
     EIGEN_DEFINE_STL_VECTOR_SPECIALIZATION(Matrix2d)
     std::vector<Vector2d>
    
    
    ```
    
    *   如果使用 C++11 以后的标准进行编译则不需要上述操作，因为 C++11 标准修复了之前标准中`std::vector`存在的问题；
    *   优点是不需要在多处指定 Eigen 定义的堆内存管理器`aligned_allocator`，缺点是需要在每次声明`std::vector`前声明宏`EIGEN_DEFINE_STL_VECTOR_SPECIALIZATION`，否则会使用默认堆内存管理器`std::allocator`造成程序崩溃；

### 将 Eigen 对象作为函数参数传递

1.  从 C++ 的角度，不建议使用值传递，因为会调用拷贝构造函数，对于大对象较为耗时，建议使用引用传递，如果不需要修改函数参数，建议使用常引用传递；
2.  从 Eigen 的角度，在将固定大小可向量化的 Eigen 对象作为函数参数传递时，不能使用值传递，应当使用引用传递，如果不需要修改函数参数，建议使用常引用传递；
    
    ```
     void my_function(Vector2d v);        // 错误
     void my_function(const Vector2d& v); // 正确
    
    
    ```
    
    ```
     struct Foo {
       Vector2d v;
     };
    
     void my_function(Foo v);                    // 错误
     void my_function(const Foo& v);             // 正确
    
    
    ```
    
    *   原因是在值传递的过程中不考虑 Eigen 对象的内存对齐修饰符（alignment modifier）；
    *   Eigen 对象作为函数返回值不受影响；

## Eigen 中的 C++11`auto`关键字问题

不建议在 Eigen 表达式中使用`auto`关键字，特别是不要使用`auto`关键字代替`Matrix`类，部分情况下存在未定义行为：

1.  可能导致表达式被重复计算；
    
    示例：
    
    ```
     MatrixXd A, B;
     auto C = A * B;         // 返回抽象表达式，表示矩阵乘积并保存到A和B的引用
     for (...) {
       w = C * v;            // 每次循环都会重复计算A * B
     }
    
    
    ```
    
2.  可能导致表达式的值发生变化；
    
    示例：
    
    ```
     MatrixXd A, B;
     auto C = A * B;         // 返回抽象表达式，表示矩阵乘积并保存对A和B的引用
     MatrixXd R1 = C;
     // do something with A
     MatrixXd R2 = C;        // A的值发生了变化，导致表达式的值发生了变化，R1≠R2
    
    
    ```
    
3.  可能导致程序崩溃；
    
    *   示例一：
        
        ```
          // eval()成员函数返回表达式的计算结果，是一个临时对象
          // transpose()成员函数返回对这个临时对象的引用
          // 执行后表达式C是一个对已被析构的临时对象的引用
          auto C = ((A + B).eval()).transpose();
          // 再使用表达式C会导致程度崩溃
          // do something with C
        
        
        ```
        
    *   示例二：
        
        ```
          VectorXd u, v;
          // normalized()成员函数会隐式调用eval()成员函数
          auto C = u + (A * v).normalized();
          // do something with C
        
        
        ```
        
4.  解决方案是对于表达式整体调用`eval()`成员函数，返回确定类型的表达式的值，如果对于一个`Matrix`类的对象调用该函数则返回常引用以避免无用拷贝；
    

## 参考

1.  [Eigen: Main Page](https://eigen.tuxfamily.org/dox/)
2.  [[QuickRef] Dense matrix and array manipulations](https://eigen.tuxfamily.org/dox/group__QuickRefPage.html)
3.  [[QuickRef] Sparse linear algebra](https://eigen.tuxfamily.org/dox/group__SparseQuickRefPage.html)
4.  [Eigen short ASCII reference](https://eigen.tuxfamily.org/dox/AsciiQuickReference.txt)
5.  [zxl19/Eigen-Cheatsheet](https://github.com/zxl19/Eigen-Cheatsheet)
6.  [gaoxiang12/slambook](https://github.com/gaoxiang12/slambook)
7.  [gaoxiang12/slambook2](https://github.com/gaoxiang12/slambook2)
8.  [qixianyu-buaa/EigenChineseDocument](https://github.com/qixianyu-buaa/EigenChineseDocument)
9.  [Aliasing](http://www.eigen.tuxfamily.org/dox/group__TopicAliasing.html)
10.  [Catalogue of dense decompositions](https://eigen.tuxfamily.org/dox/group__TopicLinearAlgebraDecompositions.html)
11.  [Linear algebra and decompositions](https://eigen.tuxfamily.org/dox/group__TutorialLinearAlgebra.html)
12.  [Benchmark of dense decompositions](https://eigen.tuxfamily.org/dox/group__DenseDecompositionBenchmark.html)
13.  [Solving linear least squares systems](http://www.eigen.tuxfamily.org/dox/group__LeastSquares.html)
14.  [LU 分解、LDLT 分解和 Cholesky 分解 - CSDN 博客](https://blog.csdn.net/zhouliyang1990/article/details/21952485)
15.  [奇异值分解（SVD）- 漫漫成长的文章 - 知乎](https://zhuanlan.zhihu.com/p/29846048)
16.  [SVD-CSDN 博客](https://blog.csdn.net/jiang_he_hu_hai/article/details/78363642)
17.  [四元数归一化 1-Stack Overflow](https://stackoverflow.com/questions/48019329/difference-between-norm-normalize-and-normalized-in-eigen)
18.  [四元数归一化 2-CSDN 博客](https://blog.csdn.net/m0_56348460/article/details/117386857)
19.  [旋转矩阵归一化 1-Stack Overflow](https://stackoverflow.com/questions/21761909/eigen-convert-matrix3d-rotation-to-quaternion)
20.  [旋转矩阵归一化 2-Stack Overflow](https://stackoverflow.com/questions/43896041/eigen-matrix-to-quaternion-and-back-have-different-result)
21.  [Alignement issues](https://eigen.tuxfamily.org/dox/group__DenseMatrixManipulation__Alignement.html)
22.  [向量化运算 - CSDN 博客](https://blog.csdn.net/weixin_38251332/article/details/120308863)
23.  [Eigen 内存对齐 1-CSDN 博客](https://blog.csdn.net/shyjhyp11/article/details/123208279)
24.  [Eigen 内存对齐 2-CSDN 博客](https://blog.csdn.net/shyjhyp11/article/details/123204444)
25.  [从 Eigen 向量化谈内存对齐 - 王金戈的文章 - 知乎](https://zhuanlan.zhihu.com/p/93824687)
26.  [Eigen 内存对齐 - 卷儿的文章 - 知乎](https://zhuanlan.zhihu.com/p/349413376)
27.  [Common pitfalls](https://eigen.tuxfamily.org/dox/TopicPitfalls.html)