---
title: An Attention-based Graph Neural Network for Heterogeneous Structural Learning
date: 2020-05-27 16:34:19
tags:
	- 图卷积
	- 异构图
categories:
	- 论文笔记
cover: http://image.rman.top/20200527165006.png
mathjax: true
typora-copy-images-to: upload
---

## Introduction

作者提出了一种在异构图上进行GNN图卷积的方式，主要是利用了对不同类型的边设定一个独有的参数$W$转到一个相同的空间，同时还用了多头的和边有关的attention机制来进行邻居信息的聚合。

## Approach

TAL层：

根据节点两个节点之间种类，用一个对应的线性变换矩阵$W$，把邻居的embedding转换到目标的空间。

![image-20200527164235504](http://image.rman.top/20200527164235.png)

邻居聚合：

![image-20200527165006142](http://image.rman.top/20200527165006.png)

1. 对不同的边的做了一个各自的线性变化$a_r$

![image-20200527165118108](http://image.rman.top/20200527165118.png)

2. 然后通过一个attention，在对邻居进行聚合。

   ![image-20200527165423020](http://image.rman.top/20200527165423.png)

3. 然后通过一个multi-head机制，同时使用多个attention参数，并把结果拼接起来，作为一个节点最后的表达。

![image-20200527165847468](http://image.rman.top/20200527165847.png)

4. 并在多层卷积的过程中加入了残差层

   ![image-20200527170201869](http://image.rman.top/20200527170201.png)

## Extension

1. 针对边和回边的问题，作者希望attention的值应该是正好相反的。

   ![image-20200527173607152](http://image.rman.top/20200527173607.png)

2. 环理论，一个j->j的转换应该等于j->i, i->i, i->j这两个应该是相等的。所以变换矩阵应该有如下的性质。

   ![image-20200527173948104](http://image.rman.top/20200527173948.png) 

i->i的矩阵是有方向的，所以需要求一个逆矩阵，这个工作很耗时，所以作者用train的方法来实现求得该逆矩阵。

![image-20200527174155225](http://image.rman.top/20200527174155.png)