---
title: Graph U-Nets
date: 2020-05-05 15:34:49
cover: http://image.rman.top/clip_image001-1588664337461.png
tags:
	- 图神经网络
	- 论文
categories:
	- 论文笔记
---

# 简介：

在传统图像领域，encoder-decoder结构，比如U-nets已经在许多图像像素领域有着许多成功的应用。然而在图像领域，由于池化和反池化操作在图领域的实现并没有自然的实现，导致这样的结构并没有在图网络中应用。本文提出了一种在图结构上**池化和反池化**的操作，池化层可以根据节点在可训练投影向量上的标量投影值，自适应地选择节点，形成较小的图。反池化层可以把池化层生成的数据恢复成带位置信息的原始结构。

# Graph Pooling Layer：

图池化，首先有节点特征X，邻接矩阵A，通过一个P向量来训练节点之间的位置关系，然后通过SIGMOD(y)+TOP-K来提取top-K特征，提取完之后，保存idex，从X，A提取特征和邻接矩阵。



![clip_image001-1588664337461](http://image.rman.top/clip_image001-1588664337461.png)

# Graph Unpooling Layer：

学习distribution，把pool过之后的K*C矩阵，和选定的节点idx，恢复成原始的N*C矩阵。

![计算机生成了可选文字: X十1=distribute(ONxc,Xc,idx）： （3）](http://image.rman.top/clip_image002.png)

只把选定位置的点设为k*C的特征，N*C的其他位置设为0