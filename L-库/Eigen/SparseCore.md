

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

    

## 模板参数

`SparseMatrix` 类和 `SparseVector` 类有 3 个模板参数：元素类型，存储顺序，`（ColMajor(默认)或RowMajor）` 和内索引类型（默认 int）。

对于密集 Matrix 类，构造函数的参数是对象的大小。

  
## 转换
任何SparseMatrix都可以通过`SparseMatrix::makeCompressed()` 方法变成稀疏模式。此时，`InnerNNZs`相对于`OuterStarts`而言，就是多余的，因`为InnerNNZs[j] = OuterStarts[j+1]-OuterStarts[j]`。因此`SparseMatrix::makeCompressed()`会释放InnerNNZ存储空间。

Eigen的操作总是返回压缩模式的稀疏矩阵。当向一个SparseMatrix插入新元素时，会变成非压缩模式。



## 支持的操作和函数

由于稀疏矩阵的特殊存储格式，它没有密集矩阵那样的灵活性。Eigen 的稀疏矩阵模块仅仅实现了密集矩阵 API 的一个子集。稀疏矩阵支持大多数的 `逐元素` 的一元操作符和二元操作符。

下面使用 sm 表示稀疏矩阵，sv 表示稀疏向量，dm 表示密集矩阵，dv 表示密集向量。

![](https://raw.githubusercontent.com/seaside2mm/github-photos/master/images/20221021170058.png )

![](https://raw.githubusercontent.com/seaside2mm/github-photos/master/images/20221021170209.png)
  


## 使用例子

```c++

SparseMatrix<std::complex<float> > mat(1000,2000);         //声明一个 1000x2000 列优先的压缩的稀疏矩阵（元素类型：complex<float>）
SparseMatrix<double,RowMajor> mat(1000,2000);              //声明一个 1000x2000 行优先的压缩的稀疏矩阵（元素类型：double）
// 迭代所有的非零元素
// 使用coeffRef(i,j)函数可以随机访问稀疏对象的元素，但是用到的二叉搜索比较浪费时间。
// 大多数情况下，我们仅需迭代所有的非零元素。这时，可以先迭代外维度，然后迭代当前内维度向量的非零元素。非零元素的访问顺序和存储顺序相同？
for (int k=0; k<mat.outerSize(); ++k)
    for (SparseMatrix<double>::InnerIterator it(mat,k); it; ++it)
    {
        it.value(); // 元素值
        it.row();   // 行标row index
        it.col();   // 列标（此处等于k）
        it.index(); // 内部索引，此处等于it.row()
    }
// 填充稀疏矩阵
// 由于稀疏矩阵的特殊存储格式，添加新的非零元素时需要特别注意。例如，随机插入一个非零元素的复杂度是O(nnz)，nnz表示非零元素的个数。
// 最简单的保证性能的创建稀疏矩阵的方法是，先创建一个三元组列表，然后转换成SparseMatrix。如下所示
typedef Eigen::Triplet<double> T;
typedef Eigen::SparseMatrix<double> SparseMatrixType;
int estimation_of_entries = 10; //预计非零元素的个数
std::vector<T> tripletList;
tripletList.reserve(estimation_of_entries);
int j = 1; // 列标
for(int i=0; i<estimation_of_entries; i++){ //遍历行
    int v_ij = i*i+1;
    tripletList.push_back(T(i,j,v_ij));
}
SparseMatrixType mat(rows,cols);
mat.setFromTriplets(tripletList.begin(), tripletList.end()); //根据三元组列表生成稀疏矩阵
直接将非零元素插入到目标矩阵的方法更加高效，节省内存。如下所示：
mat.reserve(VectorXi::Constant(cols,6)); //关键：为每一列保留6个非零元素空间
for(int i=0; i<3; i++){ //遍历行
    for(int j=0;j<3; j++){
        int v_ij = i+j+1;
        mat.insert(i,j) = v_ij;                    // alternative: mat.coeffRef(i,j) += v_ij;
    }
}
mat.makeCompressed(); //压缩剩余的空间

```

  
##  `SparseVector`

是 SparseMatrix 的一个特例，只有 `Values` 和 `InnerIndices` 数组。没有压缩和非压缩的区别。

  
 
  

# 例子

求解线性方程组：$Ax=b$。

```c++

#include <Eigen/Sparse>

#include <vector>

  

//#include <QImage> //Qt

#define M_PI       3.14159265358979323846

typedef Eigen::SparseMatrix<double> SpMat; // 声明一个列优先的双精度稀疏矩阵类型

typedef Eigen::Triplet<double> T; //三元组（行，列，值）

void buildProblem(std::vector<T>& coefficients, Eigen::VectorXd& b, int n);

void saveAsBitmap(const Eigen::VectorXd& x, int n, const char* filename);

  

int main(int argc, char** argv)

{

    assert(argc==2);

  

    int n = 300;  // 图像的尺寸

    int m = n*n;  // 未知元素的数量（等于像素数）

    // Assembly:

    std::vector<T> coefficients;            // list of non-zeros coefficients

    Eigen::VectorXd b(m);                   // 等号右边的向量b，根据约束条件生成

    buildProblem(coefficients, b, n);

    SpMat A(m,m); // 等号左边的矩阵A

    A.setFromTriplets(coefficients.begin(), coefficients.end());

    // 求解

    Eigen::SimplicialCholesky<SpMat> chol(A);  // 执行A的 Cholesky分解

    Eigen::VectorXd x = chol.solve(b);         // 使用A的Cholesky分解来求解等号右边的向量b

    // Export the result to a file:

    //saveAsBitmap(x, n, argv[1]);

    return 0;

}

  
  

void insertCoefficient(int id, int i, int j, double w, std::vector<T>& coeffs,

    Eigen::VectorXd& b, const Eigen::VectorXd& boundary)

{

    int n = int(boundary.size());

    int id1 = i+j*n;

    if(i==-1 || i==n) b(id) -= w * boundary(j); // constrained coefficient

    else  if(j==-1 || j==n) b(id) -= w * boundary(i); // constrained coefficient

    else  coeffs.push_back(T(id,id1,w));              // unknown coefficient

}

void buildProblem(std::vector<T>& coefficients, Eigen::VectorXd& b, int n)

{

    b.setZero();

    Eigen::ArrayXd boundary = Eigen::ArrayXd::LinSpaced(n, 0,M_PI).sin().pow(2);

    for(int j=0; j<n; ++j)

    {

        for(int i=0; i<n; ++i)

        {

            int id = i+j*n;

            insertCoefficient(id, i-1,j, -1, coefficients, b, boundary);

            insertCoefficient(id, i+1,j, -1, coefficients, b, boundary);

            insertCoefficient(id, i,j-1, -1, coefficients, b, boundary);

            insertCoefficient(id, i,j+1, -1, coefficients, b, boundary);

            insertCoefficient(id, i,j,    4, coefficients, b, boundary);

        }

    }

}

  

void saveAsBitmap(const Eigen::VectorXd& x, int n, const char* filename)

{

    Eigen::Array<unsigned char,Eigen::Dynamic,Eigen::Dynamic> bits = (x*255).cast<unsigned char>();

    QImage img(bits.data(), n,n,QImage::Format_Indexed8);

    img.setColorCount(256);

    for(int i=0;i<256;i++) img.setColor(i,qRgb(i,i,i));

    img.save(filename);

}

```