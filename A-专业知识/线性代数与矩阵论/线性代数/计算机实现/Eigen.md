---
title:      矩阵的python实现       
date:       2019-05-12              # 时间
author:     BY Seaside                     # 作者
categories:
- 线性代数
toc: true
---



基本运算

```c
/*
matOut   : 計算結果
matA     : 入力行列A
matARows : 入力行列Aの行数
matACols : 入力行列Aの列数
matB     : 入力行列B
matBRows : 入力行列Bの行数
matBCols : 入力行列Bの列数
*/
void addition(double **matOut, double **matA, int matARows, int matACols, double **matB, int matBRows, int matBCols){
   int i, j;
   if( (matARows != matBRows) || (matACols != matBCols) ){
      printf("計算できません\n");
      return;
   }

   for(i = 0; i < matARows; ++i){
      for(j = 0; j < matACols; ++j){
         matOut[i][j] = matA[i][j] + matB[i][j];
      }
   }
}

void subtraction(double **matOut, double **matA, int matARows, int matACols, double **matB, int matBRows, int matBCols){
   int i, j;
   if( (matARows != matBRows) || (matACols != matBCols) ){
      printf("計算できません\n");
      return;
   }

   for(i = 0; i < matARows; ++i){
      for(j = 0; j < matACols; ++j){
         matOut[i][j] = matA[i][j] - matB[i][j];
      }
   }
}

void multiplication(double **matOut, double **matA, int matARows, int matACols, double **matB, int matBRows, int matBCols){
   int i, j, k;
   if( matACols != matBRows ){
      printf("計算できません\n");
      return;
   }
   for(i = 0; i < matARows; ++i){
      for(j = 0; j < matBCols; ++j){
         matOut[i][j] = 0.0;
         for(k = 0; k < matACols; ++k){
            matOut[i][j] += matA[i][k] * matB[k][j];
         }
      }
   }
}

/*
matOut   : 出力行列
matIn     : 入力行列
matInRows : 入力行列の行数
matInCols : 入力行列の列数
*/
void transposed(double **matOut, double **matA, int matARows, int matACols){
   int i, j;
   for(i = 0; i < matARows; ++i){
      for(j = 0; j < matACols; ++j){
         matOut[j][i] = matA[i][j];
      }
   }
}
```



