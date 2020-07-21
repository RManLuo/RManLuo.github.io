---
title: 'AM-GCN: Adaptive Multi-channel Graph Convolutional Networks'
date: 2020-07-21 15:56:00
tags:
	- 图神经网络
categories:
	- 论文笔记
cover:
mathjax: true
typora-copy-images-to: upload
---

## Motivation

现有的STOA的GCN算法不能很好的将节点特征融合进拓扑结构当中。GCN在一些节点分类的任务中不能很好的融合深层的拓扑结构和节点特征。作者希望提出一种新的GCN结构，在能保持现有的GCN优点的情况下，同样能很好的融合拓扑结构和节点的特征。

## Model

<img src="http://image.rman.top/blogimage-20200717163846413.png" alt="image-20200717163846413" style="zoom:67%;" />

把图拆成两部分，生成Topology Graph和Feature Graph。

Feature Graph是根据节点的特征采用KNN构成的新的图。

然后分为三个部分，上下两个部分用独立的参数训练，中间用共享参数训练

![image-20200721153641366](http://image.rman.top/blogimage-20200721153641366.png)

![image-20200721153650181](http://image.rman.top/blogblogimage-20200721153650181.png)

然后$Z_{CT},Z_{CF}$做个两个平均值生成$Z_C$

![image-20200721153722946](http://image.rman.top/blogblogimage-20200721153722946.png)

然后对$Z_T,Z_C,Z_F$做个一个attention生成最后的表示$Z$

![image-20200721153751424](http://image.rman.top/blogblogimage-20200721153751424.png)

## Objective function

$\mathcal{L}_c$相似约束

同时对Common Convolution 的两个输出，分别生成节点内部的相似度矩阵，然后希望两个Graph的相似度矩阵足够接近。

<img src="http://image.rman.top/blogimage-20200717165002572.png" alt="image-20200717165002572" style="zoom:67%;" />

$\mathcal{L}_d$差异约束

虽然对于两个模型的$S$是相似的，但是对于两个模型的输出向量$Z$是不相似的，不然就没有学习的必要了。

![image-20200717165838333](http://image.rman.top/blogblogimage-20200717165838333.png)

$\mathcal{L}_t$分类loss

![image-20200717170107002](http://image.rman.top/blogblogimage-20200717170107002.png)