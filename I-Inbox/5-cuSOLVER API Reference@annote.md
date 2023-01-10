---
UID: 20230110175331
title: "srAnnote@cuSOLVER API Reference"
alias: ["srAnnote@cuSOLVER API Reference"]
type: Simpread
index: 5
---

# cuSOLVER API Reference

> [!timeline]+ 简介
>> **元数据**
>---
> **原文**:: [cuSOLVER API Reference](https://docs.nvidia.com/cuda/cusolver/)
> **日期**:: [[2023_01_10]]
> **标签**:: #SimpRead 
>> **摘要**
>---
> The API reference guide for cuSOLVER, a GPU accelerated library for decompositions and linear system ......

## 笔记

> [!abstract]+ <mark style="background-color: #ffeb3b">Highlight</mark> [🧷](<http://localhost:7026/reading/5#id=1673340808672>)[🌐](<#id=1673340808672>) 
> The cuSolver library is a high-level package based on the cuBLAS and cuSPARSE libraries.
^sran-1673340808672

> [!abstract]+ <mark style="background-color: #ffeb3b">Highlight</mark> [🧷](<http://localhost:7026/reading/5#id=1673340913269>)[🌐](<#id=1673340913269>) 
> The intent of cuSolver is to provide useful LAPACK-like features, such as common matrix factorization and triangular solve routines for dense matrices, a sparse least-squares solver and an eigenvalue solver. In addition cuSolver provides a new refactorization library useful for solving sequences of matrices with a shared sparsity pattern.
> 
>   
> 
> cuSolver combines three separate components under a single umbrella. The first part of cuSolver is called cuSolverDN, and deals with dense matrix factorization and solve routines such as LU, QR, SVD and LDLT, as well as useful utilities such as matrix and vector permutations.
^sran-1673340913269


