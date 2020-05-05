---
title: Representation Learning for Attributed Multiplex Heterogeneous Network
date: 2020-05-05 14:58:14
cover:  http://image.rman.top/20200505150252.png
tags:
	- 异构图
	- 论文
categories:
	- 论文笔记
---

## 简介：

现有的方法主要专注于具有单一类型节点/边缘的网络，并且不能灵活处理大型网络。许多真实世界的网络包括数十亿个节点和多种类型的边，每个节点与不同的属性相关联。本文提出了一种**带属性多通道异构图网络，**本网路结构可以支持transductive和indective的学习。

![](http://image.rman.top/20200505150252.png)

## 相关工作：

![](http://image.rman.top/20200505151154.png)

## Transductive Model: GATNE-T：

此处，模型通过边的种类，学习节点在不同边聚类下的表达。采用的是graph sage的聚集方法。

![image-20200505151637254](http://image.rman.top/image-20200505151637254.png)

然后把节点的所有边种类信息拼接起来，然后用self-attention进行聚合。

![image-20200505151656320](http://image.rman.top/image-20200505151656320.png)

## Inductive Model: GATNE-I：

为了解决一些节点之间边不存在的问题，通过节点的属性和节点的种类来对节点进行嵌入。

![image-20200505151717535](http://image.rman.top/image-20200505151717535.png)

## Meta-Random-walk:

对不同种类的边进行不同的游走，然后用负采样进行训练，最后用于节点预测。

![image-20200505151728482](http://image.rman.top/image-20200505151728482.png)