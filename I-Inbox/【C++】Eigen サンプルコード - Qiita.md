---
url: https://qiita.com/kenichi-t/items/57b5a336b20f86f45ebe
title: 【C++】Eigen サンプルコード - Qiita
date: 2023-01-19 19:33:42
tag: 
summary: 行列計算ライブラリ Eigen のサンプルコード
係数毎の計算には Array 型を使う。
---
info

More than 3 years have passed since last update.

[![](https://qiita-user-profile-images.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F101580%2Fprofile-images%2F1579846348?ixlib=rb-4.0.0&auto=compress%2Cformat&lossless=0&w=48&s=02d7d2f74c39d4d6712e0bb7bbf140fd)](https://qiita.com/kenichi-t)

posted at 2015-12-01

updated at 2019-04-27

行列計算ライブラリ Eigen のサンプルコード  
係数毎の計算には Array 型を使う。  
行列計算には Matrix 型、Vector 型、RowVector 型を使う。  
.array(), .matrix() で型変換可能。

初期化

```
Eigen::MatrixXd X(3, 3);
X << 1, 2, 3, 4, 5, 6, 7, 8, 9;
Eigen::MatrixXd X = Eigen::MatrixXd::Zero(100, 3);
Eigen::MatrixXd X = Eigen::MatrixXd::Ones(100, 3);
Eigen::MatrixXd X = Eigen::MatrixXd::Random(100, 3);
Eigen::MatrixXd X = Eigen::MatrixXd::Identity(3, 3);


```

リサイズ

```
X.resize(X.rows()+1, X.cols()); // 要素を保持しない
X.conservativeResize(X.rows()+1, Eigen::NoChange); // 要素を保持する


```

部分行列

```
Eigen::MatrixXd X = Eigen::MatrixXd::Random(10, 10);

std::cout << X << std::endl;

std::cout << X.row(3) << std::endl;
std::cout << X.topRows(5) << std::endl;
std::cout << X.bottomRows(5) << std::endl;

std::cout << X.col(3) << std::endl;
std::cout << X.leftCols(5) << std::endl;
std::cout << X.rightCols(5) << std::endl;

std::cout << X.block(2, 3, 4, 5) << std::endl;
std::cout << X.topLeftCorner(2, 3) << std::endl;
std::cout << X.topRightCorner(2, 3) << std::endl;
std::cout << X.bottomLeftCorner(2, 3) << std::endl;
std::cout << X.bottomRightCorner(2, 3) << std::endl;


```

行をランダムにシャッフル

```
Eigen::MatrixXd X = Eigen::MatrixXd::Random(100, 3);
std::cout << "X" << std::endl << X << std::endl;

// 置換行列を作成（各列と各行に唯一つの非ゼロ成分を持つ行列）
Eigen::PermutationMatrix<Eigen::Dynamic, Eigen::Dynamic> perm_row(X.rows());

// 対角要素に1をセット
perm_row.setIdentity();

// 置換行列をシャッフル
std::shuffle(perm_row.indices().data(), perm_row.indices().data() + perm_row.indices().size(), std::mt19937());

// 左からかける
Eigen::MatrixXd random_row_X = perm_row * X;
std::cout << random_row_X << std::endl;

// 元に戻す
std::cout << perm_row.inverse() * random_row_X << std::endl;


```

query との距離で行をソート

```
Eigen::MatrixXd X = Eigen::MatrixXd::Random(10, 3);
Eigen::RowVectorXd query = Eigen::RowVectorXd::Random(3);
std::cout << "X" << std::endl << X << std::endl;
std::cout << "query" << std::endl << query << std::endl;

// 距離計算
Eigen::VectorXd distance = (X.rowwise() - query).rowwise().norm();
std::cout << "distance" << std::endl << distance << std::endl;

// 置換行列を作成（各列と各行に唯一つの非ゼロ成分を持つ行列）
Eigen::PermutationMatrix<Eigen::Dynamic, Eigen::Dynamic> perm_row(X.rows());

// 対角要素に1をセット
perm_row.setIdentity();
std::cout << "sorter_row" << std::endl << perm_row.toDenseMatrix() << std::endl;

// 置換行列を距離でソート
std::sort(perm_row.indices().data(), perm_row.indices().data() + perm_row.indices().size(),
       [&distance](size_t i1, size_t i2) {return distance(i1) < distance(i2);});
std::cout << "perm_row" << std::endl << perm_row.toDenseMatrix() << std::endl;

// 転置して左からかける
Eigen::MatrixXd sorted_X = perm_row.transpose() * X;
std::cout << "sorted_X" << std::endl << sorted_X << std::endl;

// 元に戻す
std::cout << "sorted_X" << std::endl << perm_row.transpose().inverse() * sorted_X << std::endl;


```

行列 X から query との距離が 1 以下の行のみを取得

```
// 出力整形（小数点第二位まで出力）
std::cout << std::fixed << std::setprecision(2);

Eigen::MatrixXd X(5 ,3);
X << 1, 1, 1,
     1, 1, 2,
     1, 1, 3,
     1, 1, 4,
     1, 1, 5;
std::cout << "X" << std::endl <<X << std::endl;
// X
// 1.00 1.00 1.00
// 1.00 1.00 2.00
// 1.00 1.00 3.00
// 1.00 1.00 4.00
// 1.00 1.00 5.00

Eigen::RowVectorXd query(3);
query << 1, 1, 3.1;
std::cout << "query" << std::endl << query << std::endl;
// query
// 1.00 1.00 3.10

// 距離計算
Eigen::VectorXd distance = (X.rowwise() - query).rowwise().norm();
std::cout << "distance" << std::endl << distance << std::endl;
// distance
// 2.10
// 1.10
// 0.10
// 0.90
// 1.90

// 置換行列を作成（各列と各行に唯一つの非ゼロ成分を持つ行列）
Eigen::PermutationMatrix<Eigen::Dynamic, Eigen::Dynamic> perm_row(X.rows());

// 対角要素に1をセット
perm_row.setIdentity();
std::cout << "perm_row" << std::endl << perm_row.toDenseMatrix() << std::endl;
// perm_row
// 1 0 0 0 0
// 0 1 0 0 0
// 0 0 1 0 0
// 0 0 0 1 0
// 0 0 0 0 1

// 置換行列を距離でソート
std::sort(perm_row.indices().data(), perm_row.indices().data() + perm_row.indices().size(),
    [&distance](size_t i1, size_t i2) {return distance(i1) < distance(i2);});
std::cout << "perm_row" << std::endl << perm_row.toDenseMatrix() << std::endl;
// perm_row
// 0 0 0 0 1
// 0 0 1 0 0
// 1 0 0 0 0
// 0 1 0 0 0
// 0 0 0 1 0

// 転置して左からかける
Eigen::MatrixXd sorted_X = perm_row.transpose() * X;
std::cout << "sorted_X" << std::endl << sorted_X << std::endl;
// sorted_X
// 1.00 1.00 3.00
// 1.00 1.00 4.00
// 1.00 1.00 2.00
// 1.00 1.00 5.00
// 1.00 1.00 1.00

// 距離が1以下のサンプルを取得
Eigen::MatrixXd selected_X = sorted_X.topRows((distance.array() < 1).count());
std::cout << "selected_X" << std::endl << selected_X << std::endl;
// selected_X
// 1.00 1.00 3.00
// 1.00 1.00 4.00


```

統計

```
Eigen::RowVectorXd meanX = X.colwise().mean();
Eigen::RowVectorXd stdX = ((X.rowwise() - X.colwise().mean()).colwise().squaredNorm() / (X.rows() - 1)).cwiseSqrt();


```

NG 集

```
// 1xN行列やNx1行列はベクトルとみなされない
Eigen::MatrixXd meanX = X.colwise().mean();
Eigen::MatrixXd hoge = X.rowwise() - meanX; // NG : rowwise()としているのに行列を掛けている
Eigen::MatrixXd hoge = X.rowwise() - meanX.row(0); // OK


```

随時追加  
【参考】  
[Eigen 公式](http://eigen.tuxfamily.org/index.php)  
[MATLAB to Eigen](http://igl.ethz.ch/projects/libigl/matlab-to-eigen.html)

Register as a new user and use Qiita more conveniently

1.  You get articles that match your needs
2.  You can efficiently read back useful information

[What you can do with signing up](https://help.qiita.com/ja/articles/qiita-login-user)