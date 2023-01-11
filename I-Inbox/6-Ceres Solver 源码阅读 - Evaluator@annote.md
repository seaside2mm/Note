---
UID: 20230111103402
title: "srAnnote@Ceres Solver 源码阅读 - Evaluator"
alias: ["srAnnote@Ceres Solver 源码阅读 - Evaluator"]
type: Simpread
index: 6
---

# Ceres Solver 源码阅读 - Evaluator

> [!timeline]+ 简介
>> **元数据**
>---
> **原文**:: [Ceres Solver 源码阅读 - Evaluator](https://zhuanlan.zhihu.com/p/305960635)
> **日期**:: [[2023_01_11]]
> **标签**:: #SimpRead 
>> **摘要**
>---
> 本文的目的： 我之所以要阅读Ceres Solver 源码，一是了解大佬们如何写代码（自己的代码，就不多说了，很垃圾），另外一个目的，就是弄清楚为啥ceres的实现的DSO，也就是DSM 比DSO慢好多。 Ceres的代码主要包含两…

## 笔记

> [!abstract]+ <mark style="background-color: #ffeb3b">Highlight</mark> [🧷](<http://localhost:7026/reading/6#id=1673400925452>)[🌐](<#id=1673400925452>) 
> Ceres 的代码主要包含两部分，一部分是 **Problem** 的构建，换句话说就是数据的管理，优化的参数，优化的 const function, 另外一部分就是 Problem 的求解，就是 Hessian 矩阵的构建。下面我们就开始看问题的求解部分 **Evaluater。**
^sran-1673400925452


