---
title: AddGraph: Anomaly Detection in Dynamic Graph Using Attention-based Temporal GCN
date: 2020-05-26 21:12:16
tags:
	- 论文笔记
	- 异常检测
	- 动态图
categories:
	- 论文笔记
cover: http://image.rman.top/20200526211438.png
mathjax:
typora-copy-images-to: upload
---

## Introduction:

异常检测常见的领域就是在电商。比如异常的用户会通过对目标商品以及流行商品做大量的相同操作，比如同时点击目标商品和流行商品，从而增加了目标商品和流行商品的相似性。从而在推荐系统里增加了评分。这篇文章是针对动态图的异常检测，异常检测 对于下流任务很重要。 和传统的图方法相比，GCN能够自动的传播从邻居节点的信息，从而扩散节点的异常概率。GCN在异常检测方面主要的问题在于没有考虑时间特征（不能在动态图上忽略的）目前的一些工作 CAD [Sricharan and Das, 2014] and Netwalk [Yu et al., 2018] 把 graph embedding方法应用到动态图，但是不能 捕捉节点的 “长期模式”和“短期模式”。 主要贡献：

1. 提出AddGraph框架，提出一个attention-based     GRU的GCN框架，获得短时和长时特征。
2. 受到知识图谱的启发，引入一个negative     sampling 和margin loss来检测异常边

## Problem Definition:

$T$是最大的时间步长，图的集合${G^t}^T_{t=1}$,每个$G^t$代表着一个时间点子图，$A^t\in\mathbb{R}^{m\times n}$，代表每个时间点的邻接矩阵。$e = (i,j,w）\in E^t$意味着在时刻t，i和j之间存在一个权重为w的边吗，本文的目的是训练一个函数$f$，使得能对$e\in E, f(e)$能输出一个异常边的概率。


## Framework

![image-20200526212959877](http://image.rman.top/20200526212959.png)

### 图卷积

在每个时间 $t$, 我们都可对$G^t$借助$A^t$来使用GCN进行卷积，获得每个节点的隐藏层信息$H^{t-1}\in \mathbb{R}^{n \times d}$ ，同时对每层卷积层$GCN_L$计算方式如下，其实就是一个普通的卷积加了一个非线性变化。：
$$
Z^0=H^{t-1}\\
Z^l=ReLU(A^tZ^{L-1}W^{L-1})\\
Current^t=ReLU(A^tZ^{L-1}W^{L-1})
$$
### 带Attention机制的GRU门

![image-20200526215357841](http://image.rman.top/20200526215357.png)

$h_i^t$是$i$-th节点的t时间段的隐藏状态，$w$是窗口的大小，沿着w的纬度做了一个attention，得到每一个时间$t$的权重表达，简单记为
$$
Short^t_i=CAB(h_i^{t-w},\cdots,h_i^{t-1})
$$
$Current^t$是当前节点在图中的表达，$Short_t$是每个节点历史信息的表达。为了平衡这两个信息，采用了如下的门机制来融合这两个信息。

![image-20200526220302071](http://image.rman.top/20200526220304.png)

注：这个CAB等于是在传统的门机制基础上，加入了**历史一段时间的记忆**，然后和当前状态一起进入GRU更新。

预测方程：
$$
f(i,j,w)=w\cdot\sigma(\beta\cdot(||a\odot h_i+b\odot h_j||_2^2-\mu))
$$
$h_i,h_j$是两个节点在$t$的hidden特征。

Loss 采用的Margin loss，希望正例的得分小于负例的得分。

![image-20200527101145153](http://image.rman.top/20200527101154.png)