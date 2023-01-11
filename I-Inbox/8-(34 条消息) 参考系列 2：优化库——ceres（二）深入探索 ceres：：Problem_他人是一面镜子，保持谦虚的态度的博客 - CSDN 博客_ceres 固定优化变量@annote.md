---
UID: 20230111105241
title: "srAnnote@(34 条消息) 参考系列 2：优化库——ceres（二）深入探索 ceres::Problem_他人是一面镜子，保持谦虚的态度的博客 - CSDN 博客_ceres 固定优化变量"
alias: ["srAnnote@(34 条消息) 参考系列 2：优化库——ceres（二）深入探索 ceres::Problem_他人是一面镜子，保持谦虚的态度的博客 - CSDN 博客_ceres 固定优化变量"]
type: Simpread
index: 8
---

# (34 条消息) 参考系列 2：优化库——ceres（二）深入探索 ceres::Problem_他人是一面镜子，保持谦虚的态度的博客 - CSDN 博客_ceres 固定优化变量

> [!timeline]+ 简介
>> **元数据**
>---
> **原文**:: [(34 条消息) 参考系列 2：优化库——ceres（二）深入探索 ceres::Problem_他人是一面镜子，保持谦虚的态度的博客 - CSDN 博客_ceres 固定优化变量](https://blog.csdn.net/hltt3838/article/details/119776801)
> **日期**:: [[2023_01_11]]
> **标签**:: #SimpRead 
>> **摘要**
>---
> 1-1 ???? Problem类函数总览整个Problem函数内部的核心操作实际上是由类对象内部的internal::scoped_ptr&lt;internal::ProblemImpl&gt; problem_impl_; 操作的，这层关系就好比STL 提供的 queue 和stack和deque的关系，queue 和stac的内部实际上都是deque实现的专业术语叫做配接器 (adapters);秀一把C++概念（可不看） 容器 (containers) : 各种数据结构，如 vect

## 笔记

> [!abstract]+ <mark style="background-color: #ffeb3b">Highlight</mark> [🧷](<http://localhost:7026/reading/8#id=1673401961422>)[🌐](<#id=1673401961422>) 
> ![](https://img-blog.csdnimg.cn/20210818114027777.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hsdHQzODM4,size_16,color_FFFFFF,t_70)
^sran-1673401961422


