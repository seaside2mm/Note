---
UID: 20230111104653
title: "srAnnote@(34 条消息) 优化库——ceres（一）快速概览_雨 luo 凡城的博客 - CSDN 博客_ceres 优化"
alias: ["srAnnote@(34 条消息) 优化库——ceres（一）快速概览_雨 luo 凡城的博客 - CSDN 博客_ceres 优化"]
type: Simpread
index: 7
---

# (34 条消息) 优化库——ceres（一）快速概览_雨 luo 凡城的博客 - CSDN 博客_ceres 优化

> [!timeline]+ 简介
>> **元数据**
>---
> **原文**:: [(34 条消息) 优化库——ceres（一）快速概览_雨 luo 凡城的博客 - CSDN 博客_ceres 优化](https://blog.csdn.net/jdy_lyy/article/details/119336403)
> **日期**:: [[2023_01_11]]
> **标签**:: #SimpRead 
>> **摘要**
>---
> 文章目录1 :book: ceres快速概览1.1 :bookmark:问题建模和求解1.2 ::bookmark:ceres使用流程1.3 :bookmark: 求导方法：构建代价函数（STEP2）1.3.1 解析求导（自定义求导）1.3.2 其他求导1.4 :bookmark: 构建优化问题并求解(STEP3)参考链接1 ???? ceres快速概览基本概念对于任何一个优化问题，我们首先需要对问题进行建模，之后采用合适的优化方法，进行求解。在求解的过程中，往往需要进行梯度下降求取最优，这里涉及了

## 笔记

> [!abstract]+ <mark style="background-color: #ffeb3b">Highlight</mark> [🧷](<http://localhost:7026/reading/7#id=1673401683321>)[🌐](<#id=1673401683321>) 
> ![](https://img-blog.csdnimg.cn/3fab2f7272ba41c4afb928a82cff5a4c.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pkeV9seXk=,size_16,color_FFFFFF,t_70)
^sran-1673401683321

> [!abstract]+ <mark style="background-color: #ffeb3b">Highlight</mark> [🧷](<http://localhost:7026/reading/7#id=1673401726803>)[🌐](<#id=1673401726803>) 
> ![](https://img-blog.csdnimg.cn/f30e19cb58184183819ef22cf2342275.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pkeV9seXk=,size_16,color_FFFFFF,t_70)
> 
> 使用 Ceres Solver 求解非线性优化问题，主要包括以下几部分：
> 
> *   【STEP 1】构建优化问题 (`ceres::Problem`)
>     
> *   【STEP 2】构建代价函数 (`ceres::CostFunction`) 或残差 (residual)
>     
> *   【STEP 3】配置代价函数和损失函数 (`ceres::Problem::AddResidualBlock`)：通过 `AddResidualBlock` 添加代价函数 (cost function)、损失函数 (loss function) 和待优化状态量
>     
>       
>       
>     
>     **LossFunction**: a scalar function that is used to reduce the influence of outliers on the solution of non-linear least squares problems.
>     
>       
>     
> *   【STEP 4】配置求解器 (`ceres::Solver::Options`)
>     
> *   【STEP 5】运行求解器 (`ceres::Solve(options, &problem, &summary)`)
>
^sran-1673401726803
