---
title: Heterogeneous Graph Transformer
date: 2020-06-02 16:52:22
tags:
	- Transformer
	- 异构图
categories:
	- 论文笔记
cover: http://image.rman.top/20200602234759.png
mathjax: True
typora-copy-images-to: upload
---

## Abstract

在传统的GNN中，所有的节点和边都属于一个种类，导致一些传统的GNN手段没有办法适应异构图的结构。在这篇文章中作者提出了一种基于异构图的transformer结构，用来生成不同种类的节点、边的attention值。为了解决任意时间的动态图，作者提出了一个 relative temporal encoding technique。同时为了解决大量的 图数据库，作者提出了一种基于mini-batch的图采样算法，来在图上进行高效的训练。

## Current Problem

1. 基于meta-path的方法需要专家知识
2. 简单的假设所有的边和节点都有相同的特征表示空间
3. 对不同的边采用了不同的权重矩阵，有大量的种类的边和节点，同时不同种类的边和节点出现的次数也不一样，对出现数量较少的边可能不是很好建模
4. 忽视了图的动态性

## Contribution

1.  针对Heterogeneous Graph 边和点的种类数目过多的问题，作者采用对Meta Relation建模的，方式，通过起点和终点，以及边的种类来对一个meta relation建模。这样可以使用较少的参数。
2. 通过神经网络结构来捕获高阶的异构图邻居特征，自动的学习meta path的重要性
3. 通过relative temporal encoding technique来对动态的异构图进行建模
4. 通过图采样的方式，在超大的图上进行了实验

## Approach

### Definition

Heterogeneous Graph:

$G=(\mathcal{V,E,A,R})$

$\mathcal{V}$：节点

$\mathcal{E}$：边

$\mathcal{A}$：邻接矩阵

$\mathcal{E}$：边的种类

Meta Relation：

$<\tau(s),\phi(e),\tau(t)>$

Dynamic Heterogeneous Graph

$e=(s, t)$ with timestamp $T$, denote $s,t$ connected in $T$

### HGT Architecture

![image-20200602234752259](http://image.rman.top/20200602234759.png)

HGT希望能从源节点和目标节点直接获取上下文的表示，整个过程可以分为：

Heterogeneous Mutual Attention，Heterogeneous Message Passing，Target-Specific Aggregation

三个部分

### Heterogeneous Mutual Attention：

calculate the mutual attention between source node $s$ and target node $t$ .

![image-20200602235652186](http://image.rman.top/20200602235652.png)

Attention: estimates the importance of each source node

Message: extracts the message by using only the source node $s$;

Aggregate: leverages the information from its neighbors

Map target node $t$ into a Query vector, and source node $s$ into a Key vector and calculate their dot product as attention.

![image-20200603001948473](http://image.rman.top/20200603001948.png)

整体结构和传统的transformer类似，只不过对不同的边有一个$W_{\phi(e)}^{ATT}$进行了线性变化。然后把所有的pair结果合并起来得到对不同meta的attention值。