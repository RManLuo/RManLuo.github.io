---
title: Distance Encoding – Design Provably More Powerful Graph Neural Networks for Structural Representation Learning
date: 2020-11-13 22:09:26
tags:
	- 图神经网络
categories:
	- 论文笔记
cover: http://image.rman.top/blogimage-20201113224654272.png
mathjax: false
typora-copy-images-to: upload
---

# The problem of GNNs:

- 传统GNN会被1-WL     test 所限制。因为节点都是以度进行区分的。
- 核心问题：节点分类或者连接预测并不是同构问题，但是GNN是基于WL-test的所以必须要给节点引入特征。
![image-20210220115605821](https://image.rman.top/blog20210220115605.png)
- 传统的WLtest会根据节点的度来区分节点，就会导致无法区分结构信息
![image-20210220115734405](https://image.rman.top/blog20210220115734.png)
- 在这里做两层卷积会导致节点信息都是相同，无法直接区分两个节点之间是否有连边。
![image-20210220115706653](https://image.rman.top/blog20210220115706.png)
- 全图分类会有无法区分
![Suppose we query two entire graphs. Can WLGNNs distinguish them? ](http://image.rman.top/blogclip_image004.png)

# Distance Encoding:

- 采用最短路径作为特征，可以区分节点同构

![Suppose we query these two red node. Can WL-GNNs  distinguish them within 2 layer? ](http://image.rman.top/blogclip_image005.png)

![image-20210220115813584](https://image.rman.top/blog20210220115813.png)

- 同时也可以解决链接预测的问题。对每个pair都对图中的所有节点算最短距离。

![image-20201113224654272](http://image.rman.top/blogimage-20201113224654272.png)

- 对于全图的预测来说

- 对每个节点都进行标记，然后就能区分

![Suppose we query two entire graphs. Can WLGNNs distinguish them?  blue nth distanæ while all paiß  Of green with distance ](http://image.rman.top/blogclip_image008.png)

 ![Distance encoding  • A target node subset S, and a node u in V  • Distance Encoding:  where  - (Wuv, WK, ... , wk)  ((ulv) = ), I  uv uv  W is the random walk matrix: W = AD -l  Shortest-Path-Distance (SPD), personalized pageRank scores, Katz  similarity are all special cases.  In practice, k: 2—4. ](http://image.rman.top/blogclip_image009.png)


![Take-away I:  1.  2.  3.  4.  5.  6.  A target node subset S, and a node u in V  Extract the ego-net around S.  Use BFS to compute shortest path distance (SPD) or random walk  matrix power WK between each node v in S and u in the ego-net.  You obtain ((ulv). If SPD> threshold, set a default value.  Use one-hot encoding of  Concate < (ulS) with the original node attributes and then use WL-  GNNs over this ego-net. ](http://image.rman.top/blogclip_image010.png)
- 采用one-hop标记，dimension最大是要卷积的距离，比如最大距离是4，则有4为0000，比如有两个节点到他的距离分布是2和3，则最后距离嵌入表示是0110，如何有两个节点是距离为4则是0002，以此类推。
- 得到feature就把它拼接到原始节点上进行卷积

### 证明：

![The Power of DE-GNN for p-sized node-set  Theorem 3.3  • Two structures (SI,AI) and (S2,A2) with ISII= IS21 = p, p fixed:  1.  2.  3.  Al and AZ are only different in structures (features are all the same)  that are uniformly independently sampled from r-regular graphs (r <  (210g n)AO.5)  DE-GNN (with some injective requirement) within layers L <  (0.5+e) log  can distinguish (SI,AI) and (S2,A2) with probability 1-  O(l/n)  Distance encoding can be simply chosen as shortest-path-distance ](http://image.rman.top/blogclip_image011.png)
- 对于层数不断增加，则区分度更好。
![Empirical evaluation  Simulation:  Uniformly sample 104/n many  n-sized 3-regular graphs.  Compare all pairs of nodes (u,v)  Use an untrained DE-GNN after  L layers with randkm  initialization.  The color of the points implies  the portion of pairs of u,v such  that — le — 12.  O  0.5 logn  simulation  1.0  0.8  0.6  0.4  0.2  0.0 ](http://image.rman.top/blogclip_image012.png)

### 实验：

- 实验是在以节点周围结构为准的图来进行判断。