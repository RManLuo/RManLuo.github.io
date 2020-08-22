---
title: Redundancy-Free Computation for Graph Neural Networks
date: 2020-08-22 20:20:26
tags:
	- 图神经网络
	- 图数据库
categories:
	- 论文笔记
cover: http://image.rman.top/blogimage-20200821163547510.png
mathjax: false
---

# Redundancy-Free Computation for Graph Neural Networks

## Motivation

* To avoid redundant computations：减少冗余计算

* HAGs are functionally equivalent to standard GNN-graphs：性能不变

* An agnostic method：对所有模型都适用

  ![image-20200821163547510](http://image.rman.top/blogimage-20200821163547510.png)

对邻居上重合的的vector进行聚合，然后再传递。

## HAGs

把聚合的点和原来的点聚合，生成一张新图

![image-20200821163906650](http://image.rman.top/blogimage-20200821163906650.png)

## Existing GNNs

* 没有顺序：GCN
* 有顺序：LSTM聚合

## Aggregation Nodes

![image-20200821164422651](http://image.rman.top/blogimage-20200821164422651.png)

* 如果是原图的属性，就用节点的隐藏向量

* 如果是聚合节点，就用聚合向量。

![image-20200821164632482](http://image.rman.top/blogimage-20200821164632482.png)

## Cost Function

![image-20200821165644760](http://image.rman.top/blogimage-20200821165644760.png)

## Algorithm

![image-20200821165855440](http://image.rman.top/blogimage-20200821165855440.png)