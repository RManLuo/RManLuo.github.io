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

$\mathcal{R}$：边的种类

Meta Relation：

$<\tau(s),\phi(e),\tau(t)>$

Dynamic Heterogeneous Graph

$e=(s, t)$ with timestamp $T$, denote $s,t$ connected in $T$

### HGT Architecture

![image-20200602234752259](http://image.rman.top/20200602234759.png)

HGT希望能从源节点和目标节点直接获取上下文的表示，整个过程可以分为：

Heterogeneous Mutual Attention，Heterogeneous Message Passing，Target-Specific Aggregation

三个部分

#### Heterogeneous Mutual Attention：

calculate the mutual attention between source node $s$ and target node $t$ .

![image-20200602235652186](http://image.rman.top/20200602235652.png)

Attention: estimates the importance of each source node

Message: extracts the message by using only the source node $s$;

Aggregate: leverages the information from its neighbors

Map target node $t$ into a Query vector, and source node $s$ into a Key vector and calculate their dot product as attention.

![image-20200603001948473](http://image.rman.top/20200603001948.png)

整体结构和传统的transformer类似，只不过对不同的边有一个$W_{\phi(e)}^{ATT}$进行了线性变化。然后把所有的pair结果合并起来得到对不同meta的attention值。

#### Heterogeneous Message Passing

For a pari of nodes $e=(s, t)$, we calculate its multi-head Message by:

![image-20200603092319904](http://image.rman.top/20200603092320.png)

为了获得$i-th$个message head $MSG-head^i(s,e,t)$，作者首先先把$\tau(s)$-type的source node映射到$i$-th message的空间，使用一个$W^{MSG}$矩阵。

#### Target specific Aggregation

在得到了节点attention和节点message的表达之后，我们可以简单的把attention值和message相乘，然后相加。

![image-20200603093155025](http://image.rman.top/20200603093155.png)

然后通过一个线性变换矩阵，来把target node变换到其目标的空间。

![image-20200603093830805](http://image.rman.top/20200603093830.png)

### Relative Temporal Encoding

作者提出了一个 Relative Temporal Encoding 机制，受到Transformer的 position encoding影响，可以对节点和图在不同时刻进行建模。

给定一个源节点$s$和一个目标节点$t$，在一个时间$T$内，相对的time gap$\bigtriangleup T(t,s)=T(t)-T(s)$作为其在时间上的encoding $RTE(\bigtriangleup(t,s))$. 但是由于这种方法不一定能cover所有的时间，所以作者还是借助了传统transformer的正弦函数，配合$\bigtriangleup$来构建时序的encoding。这样做融合了离散和连续两个时间戳表示方法。

![image-20200603131657531](http://image.rman.top/20200603131657.png)

![image-20200603131717886](http://image.rman.top/20200603131717.png)

### HGSampling

为了解决传统的图算法不能支持大量的图计算的问题，作者提出了一种基于图采样的算法。整体思想为：为每一个节点类型构造一个budget $B[\tau]$，然后采样相同的数量的节点。给定一个已经采样的节点$t$，我们把他的所有直接邻居加入对应的budget，然后更新其被采样的概率，并通过度数进行归一化，防止采样过多度数较高的节点。然重复L次，直到采样了所有节点的L阶邻居。

<img src="http://image.rman.top/20200603095308.png" alt="image-20200603095308068" style="zoom:67%;" />

#### Inductivev Timestamp Assignment

在异构图中，一个节点往往可能会有多个timestamp，作者要解决选择给节点加入什么时刻的timestamp，解决方法也很直接：

	1. 对于有着固定时间的node，比如论文，有一个唯一的发表时间，则就把该时间作为论文的timestamp
 	2. 对于没有固定时间的node，比如一个会议，则以与他相关的那个的节点的时间作为这个会议的timestamp

![image-20200603104412682](http://image.rman.top/20200603104412.png)

![image-20200603104424572](http://image.rman.top/20200603104424.png)

## Experiment

作者在开放学术图谱（OAG）上进行试验。该数据集包含 1.79 亿个节点和 20 亿个边组成，时间跨度从 1900 年到 2019 年。实验结果表明，与传统的 GNNs 和异构图模型相比，在下游任务中 HGT 可以显著提高 9-21%。

![image-20200603105557029](http://image.rman.top/20200603105557.png)

同时，利用提出的相对时间编码（RTE），我们可以动态地计算出任意一个年份的节点表示。例如，通过距离，我们可以观测出每个会议在不同时间其相似会议的变化。如下图所示，WWW 在 2020 年与一些网络、数据库的会议更接近，而在 2020 年却与一些数据挖掘的会议更接近。

![image-20200603105925633](http://image.rman.top/20200603105925.png)

同时，作者还验证了 HGT 可以隐性地抽取出对下游任务重要的元路径，而不需要人为定义。例如下图中的 <paper, is_published_at, venue, is_published_at-1, paper> 路径就有着最高的重要性。

![image-20200603110029430](http://image.rman.top/20200603110029.png)