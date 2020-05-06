---
title: Neural Graph Collaborative Filtering
cover: http://image.rman.top/20200506161540.png
tags:
	- 推荐系统
	- 图神经网络
	- 论文
categories:
	- 论文笔记
---

# Abstract：

传统的深度学习协同过滤，ID的嵌入是直接输入交互层，但是在NGCF中，其能捕获用户和物品之间的高阶关系。

![计算机生成了可选文字: User-itemInteractionGraph High-orderConnectivityfor榄1 Figure1：Anillustrationoftheuser-iteminteractiongraph andthehigh-orderconnectivity.Thenode屿isthetarget usertoproviderecommendationsfor.](http://image.rman.top/20200506225252.jpg)

 

# Method：

## 假设：

Intuitively, the interacted items provide direct evidence on a user’s preference [16, 38]; analogously, the users that consume an item can be treated as the item’s features and used to measure the collaborative similarity of two items.

 

## Message Construction:

$e_i$,$e_u$ 做哈达玛积，然后通过$W_2$ 转换，然后和$e_1$ 通过$W_1$ 做转换之后相加。然后除以度数乘积进行归一化。

![](http://image.rman.top/20200506225439.png)

## Message Aggregation:

邻居信息相加聚合在加到用户自身上和用户相加通过激活函数。

![clip_image004](http://image.rman.top/20200506225702.png)

## 多层次卷积：

很典型的图卷积矩阵定义

![clip_image005](http://image.rman.top/20200506225703.png)

## Model prediction:

每一层都会得到用户u的表示，将其拼接得到用户最后的表示。

![clip_image006](http://image.rman.top/20200506225704.png)

同理得到物品的表示，然后点积预测结果。

Loss采用BPR loss

Pairwise。对于implicit feedback（如是否购买），BPR对每一个user建立一个偏序关系 

 。如图所示，user-item矩阵中，+表示用户购买了该商品，?表示没有购买。比如，对user 1来说，他购买了 

，没有购买 

，则对于他来说，2和3比1和4好，但2、3之间，1、4之间无法比较

 

来自 <[*https://zhuanlan.zhihu.com/p/31841042*](https://zhuanlan.zhihu.com/p/31841042)>

![clip_image007](http://image.rman.top/20200506225705.png)