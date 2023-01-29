

# 线性方程组的求解

## 2. LU分解与高斯消元

> 高斯消元法求解LU. 要求A可逆

- 置换矩阵

$P^{-1}=P^T$, 因为P正交矩阵

```matlab
A =
    10    -7     0
    -3     2     6
     5    -1     5

p =
     1     3     2

>> A(p,:)   %PA
    10    -7     0
     5    -1     5
    -3     2     6
    
>> A(:,p)   %AP
    10     0    -7
    -3     6     2
     5     5    -1
```

### Matlab求解$Ux=b$

> 求解 n-by-n **upper triangular** system。 明显需要从最后一行求得$x_n$，再依次回溯。

```matlab
U =
     1     2     3
     0     4     5
     0     0     6
b =
     1
     2
     3
>>>> x = U\b
x =

   -0.2500
   -0.1250
    0.5000
```

- One subtracts multiples of the columns of U from b.

```matlab
x = zeros(n,1); %step 1:初始化x=zeros(n,1);
for k = n:-1:1
  x(k) = b(k)/U(k,k);        %step2: 计算最后一行x(k)
  i = (1:k-1)’;              %对前面的行i，
  b(i) = b(i) - x(k)*U(i,k); %向量计算，前去x(k)这个分量乘以相应系数
end
```

- The other uses inner products between the rows of U and portions of the emerging solution x.

```matlab
x = zeros(n,1);
for k = n:-1:1
  j = k+1:n;
  x(k) = (b(k) - U(k,j)*x(j))/U(k,k);
end
```

### matlab

```matlab
function x = gausselimination(A, b)
% 针对线性方程组 Ax=b问题求解
% 选主元的高斯消去法
% A=[4 -2 -3 6; -6 7 6.5 -6;1 7.5 6.25 5.5 ; -12 22 15.5 -1];
% b =[12; -6.5; 16; 17] ;
% x = gausselimination(A, b)

matAb =[A, b];%系数矩阵和右端项组成的矩阵

[rows, cols]= size(matAb);%行数，列数

for row =1: rows-1 %消去过程
    %寻找主元列中对角线下面绝对值最大的元素
    [pivot_max, index_max]=max(abs(matAb(row : rows, row))); %当前主元所在列的最大项和索引
    index_max=index_max+row-1; %修正为正确的行索引因为上面返回的是相对于对角线的索引号,
    %如果对应元素不是0 如果是的话那么[0就是奇异阵了A]
    if(matAb(index_max, row) ~= 0)
        if(index_max ~= row)
            matAb([row index_max], : ) = matAb([index_max row ], : );%进行行交换
        end
    end

    %计算乘子更新主元行以下的行
    for i = row+1: rows
        %注意是当前行元素减去乘子乘以主元行对应元素
        matAb(i, row : cols ) = matAb (i, row : cols )-matAb(i, row)/matAb(row, row)*matAb(row, row : cols);
    end
end

%回代计算解
x= zeros(rows, 1); %解的列向量
x(rows) =matAb(rows , cols)/matAb(rows, rows); %最后一个解

for i = rows-1:-1:1 %自底向上回代
%右端项减去已经求出的部分然后除以它的系数
    x(i)=(matAb(i, cols)-matAb(i,i+1:rows)*x(i+1: rows))/matAb(i, i);
end
```



```matlab
function [L,U,p] = lutx(A)
%LUTX  Triangular factorization, textbook version
%   [L,U,p] = lutx(A) produces a unit lower triangular matrix L,
%   an upper triangular matrix U, and a permutation vector p,
%   so that L*U = A(p,:)

% A=[4 -2 -3 6; -6 7 6.5 -6; 1 7.5 6.25 5.5 ; -12 22 15.5 -1];
% b =[12; -6.5; 16; 17 ] ;
% [L ,U, P]= lugs(A)
% A(P, : )
% L*U

[n,n] = size(A);
p = (1:n)';

for k = 1:n-1
   %查找主元列对角线以下的绝对值最大的元素和索引
   % Find index of largest element below diagonal in k-th column
   [r,m] = max(abs(A(k:n,k)));
   m = m+k-1;

   % Skip elimination if column is zero 如果对应元素是则跳过消去过程0
   if (A(m,k) ~= 0)
   
      % Swap pivot row满足条件的话就交换行
      if (m ~= k)
         A([k m],:) = A([m k],:);
         p([k m]) = p([m k]);  %对应的排列阵也要跟着变化
      end

      % Compute multipliers
      i = k+1:n;
      A(i,k) = A(i,k)/A(k,k);

      % Update the remainder of the matrix
      j = k+1:n;
      A(i,j) = A(i,j) - A(i,k)*A(k,j);  %就地存储！
   end
end

% Separate result
L = tril(A,-1) + eye(n,n); %下三角部分，但是对角线上都是1
U = triu(A);
```

### python

[lu.py](https://github.com/seaside2mm/linalg/blob/master/src/lu.py)

## Cholesky分解

![截屏2020-05-11 21.29.21](https://tva1.sinaimg.cn/large/007S8ZIlgy1geosbbcxszj31040u0q6f.jpg)





### 线性代数中一些基础算法的python实现

> 用python实现了常用的线性代数的基础算法.python2.7跑例子可以通过。很多细节没有考虑

> 主要用来学习线性代数的算法原理. eig求解时，用到迭代算法.

> 在代码里也有一些注释

#### 1. 方阵求逆

> 采用类似LU分解的方法求矩阵的逆。

> [inv.py](https://github.com/seaside2mm/linalg/blob/master/src/inv.py)



#### 3. 求方阵A的左零空间的基

> 主要用来求解 Ax = 0的基础解。类似高斯消元法，求解给出了零空间的一组标准正交基. A的行空间和零空间正交

> [null_space.py](https://github.com/seaside2mm/linalg/blob/master/src/null_space.py)

#### 4. 解线性方程组

> 求解 Ax = b. 使用LU分解求解。 要求A是非奇异的。

> [solve.py](https://github.com/seaside2mm/linalg/blob/master/src/solve.py)

#### 5. QR分解

> 使用 Gram-Schmidt正交化对矩阵进行QR分解

> [qr.py](https://github.com/seaside2mm/linalg/blob/master/src/qr.py)

#### 6. QR分解求解矩阵的特征值和特征向量

> 考虑了重根的情况。但没有考虑虚根等等

> [eig_by_qr.py](https://github.com/seaside2mm/linalg/blob/master/src/eig_by_qr.py)

#### 7. 幂法、逆幂法求解最大、最小特征值

> [eig_by_pow_inverse.py](https://github.com/seaside2mm/linalg/blob/master/src/eig_by_pow_inverse.py)

#### 8. 对角化

> A=PDP-1分解算法

> 对矩阵A先求特征值和向量，然后进行对角化

> [diagonalization.py](https://github.com/seaside2mm/linalg/blob/master/src/diagonalization.py)

#### 9. SVD分解

> 先用eig_by_qr方法求解A.T * A的特征值和特征向量，然后进行SVD分解

> SVD分解应用比较广，实际并不用eig_by_qr来求解(方程容易病态), 用迭代法比较多

> [svd.py](https://github.com/seaside2mm/linalg/blob/master/src/svd.py)

#### 10. 求伪逆

> 实现了两个算法。 QR分解和SVD求伪逆. 其中QR分解要求A阵列线性无关

> [pinv.py](https://github.com/seaside2mm/linalg/blob/master/src/pinv.py)

#### 11. 最小二乘法

> 使用正规方程、QR分解、SVD分解、多项式拟合求解最小二乘

> SVD分解直接用的SVD求伪逆函数

> [lstsq.py](https://github.com/seaside2mm/linalg/blob/master/src/lstsq.py)



# 矩阵特征值和奇异值的求解

## 求矩阵特征值

### 幂法

![截屏2020-05-11 23.12.16](https://tva1.sinaimg.cn/large/007S8ZIlgy1geov9b02foj31dw0te499.jpg)

###  反幂法和移位幂法



### QR 分解的迭代过程



## 计算矩阵的奇异值

