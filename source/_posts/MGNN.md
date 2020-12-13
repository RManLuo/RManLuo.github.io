---
title: Beyond Clicks Modeling Multi-Relational Item Graph for Session-Based Target Behavior Prediction
date: 2020-12-13 15:07:18
tags:
	- 图神经网络
	- 会话推荐
	- 序列推荐
	- 推荐系统
categories:
	- 论文笔记
cover: http://image.rman.top/blog20201213153038.png
mathjax: true
typora-copy-images-to: upload
---

## Background

**Session recommendation:** 

Aiming to predict the next item to be interacted with a user under a specific type of behavior, and modeling user dynamic interest.

**Target behavior session recommendation:** 

Considers target behavior and auxiliary behavior sequences and explores for accurate prediction.

![Next  Session recommendation  Target behavior is sparse but important.  Next  Target behavior session recommendation ](http://image.rman.top/blog20201213153011.png)

## Challenges

- Firstly, most existing  methods focus on only using the same type of user behavior as input for the next item prediction, but ignore the potential of leveraging other  type of behavior as auxiliary information.
- Secondly,  item-to-item relations are modeled separately and locally, since both RNN based and GNN based recommendation models only utilize one behavior sequence each time.

## Contribution

- We break the restriction of only using one type of  behavior sequence in session-based recommendation and **exploring  another type of behavior** as auxiliary information and construct the multirelational item graph for learning global item-to-item relations.
- we develop the novel graph model MGNN-SPred which learns **global item-to-item relations** through graph neural network and integrates representations of target and auxiliary of current sequences by the gating mechanism.
- We carry **out extensive experiments** and demonstrate MGNNSPred achieves the best performance among strong competitors.

## Method

![img](http://image.rman.top/blog20201213153038.png)

### Problem Definition

• Session set: $S$
• Target behavior sequence: $P^s=[p_1^s,⋯,p_|P^s |^s]$
• Auxiliary behavior sequence: $Q^s=[q_1^s,⋯,q_(|Q^s |)^s]$ 
• Multi-Relational Item Graph: $G=(V,E)$, $V$ is the set of nodes in the
• graph containing all available items and $E$ is the edge sets involving
• Multiple types of directed edges. Each edge is a triple consisting of the head item, the tail item, and the type of this edge. Edge $(a, b, click) ∈ E$ means that a user clicked item $b$ after clicking item $a$.

### Graph Construction

The algorithm browses all behavior sequences, collects all items in the sequences as the nodes of the graph, and constructs edges between two consequent items in the same sequence with their behavior types as the edge types.

![image-20201213154019948](http://image.rman.top/blog20201213154029.png)

### Item Representation Learning

For each node $v∈V$, first convert them to a space $E∈\mathbb{R}^{|V|×d}$, and feed them with MRIG into GNN.

Each node in the graph has four types of neighboring node sets. According to the type and direction, we name the four sets as “target-forward”, “target-backward”, “auxiliary-forward”, and “auxiliary-backward”.

![image-20201213154129052](http://image.rman.top/blog20201213154129.png)

Same for the auxiliary set.

### Item Representation Learning

First aggregate each group of neighbors by mean-pooling to obtain the representation of this group, defined as below:

![image-20201213154152657](http://image.rman.top/blog20201213154152.png)

Then, we combine these four representations of different neighbor groups by sum-pooling:

![image-20201213154216522](http://image.rman.top/blog20201213154216.png)

Finally, we update the representation of the center node 𝑣 by:

![image-20201213154157722](http://image.rman.top/blog20201213154157.png)

### Sequence Representation Learning

Mean pooling the target behavior sequence $𝑃$ and auxiliary behavior sequence $𝑄$ as $p$ and $q$, respectively, which are given as:

![image-20201213154243403](http://image.rman.top/blog20201213154243.png)

where $g_v=h_v^K$, and $K$ is the total iterations of the GNN.

A gate mechanism to calculate the relative importance weight $𝛼$

$$
α=σ(W_g [p;q])
$$

$o$ for the current session by the weighted summation of $p$ and $q$:
$$
o=α ⋅p+(1-α)⋅q
$$

### Model Prediction and Training

We further calculate the recommendation score $𝑠_𝑣$ of each item $v ∈V$ using the item embedding $e_v$ . A bi-linear matching scheme is employed by:

$$
s_v=o^⊤ We_v
$$

Over all items to get the probability distribution $\hat{y}$:

$$
\hat{y}=softmax(s)
$$

Loss function:

![image-20201213154515710](http://image.rman.top/blog20201213154515.png)

## Experiment

### Datasets:

![image-20201213154533984](http://image.rman.top/blog20201213154534.png)

### Baselines:

![image-20201213154542945](http://image.rman.top/blog20201213154543.png)

#### Results

![image-20201213154559738](http://image.rman.top/blog20201213154559.png)

![image-20201213154603602](http://image.rman.top/blog20201213154603.png)

![image-20201213154606397](http://image.rman.top/blog20201213154606.png)

![image-20201213154610835](http://image.rman.top/blog20201213154610.png)

![image-20201213154614279](http://image.rman.top/blog20201213154614.png)