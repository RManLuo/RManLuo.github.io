---
title: GHashing Semantic Graph Hashing for Approximate Similarity Search in Graph Databases
date: 2020-11-13 22:09:49
tags:
	- 图神经网络
	- 图数据库
categories:
	- 论文笔记
cover: http://image.rman.top/blog20201114133609.png
mathjax: true
typora-copy-images-to: upload
---

## Objection：

- Retrieve graphs from the database similar enough to a query.

- - Optimize the pruning stage.

## Background:

- Graph Edit Distance (GED) is a method to measure the distance between two graphs.
- The current similarity search, first compute the lower bound between the query and the every candidate graph and they prune the graph larger tan than the threshold. Then, they compute the exact GED in the remaining graphs.

## Challenges:

- The computation of Graph Edit Distance (GED) is NP-hard.

- Existing methods cannot handle the large databases due to their exact pruning strategy.

- - Subgraph isomorphism check to generate the graph index
  - GED lower bounds are too  loose, which leads to many candidates.

## Deep hash retrieval:

![image-20201114133540575](http://image.rman.top/blog20201114133540.png)

![Target  K: categ  00 Ι Ι0Ι0Ι0ΙOΙ  ΙΙ0ΙΙ0Ι0Ι0  οιοιιοιοιοοι  001101101010 ](http://image.rman.top/blog20201114133543.png)

## Graph Deep Hash:

- Train a GNN as hash function to generate the index of the graph, where the similarity computation can     be done on the hash index instead of the whole graph.
- Prune the graphs using the hash code and embeddings.
- Compute the exact GED using GED solver.

![Graph  Conv  Graph  Conv  Graph  Graph  Conv  P ooling  Offline  Stage  Online  Stage  Graph Feature Extraction  Embe dding  Loss.*  Embedding  Embedding  Binarizatlon  Graph  Graph  Graph  Graph  A ttenti on  Ha sh Index  F(go  F(gJ  Pluning Phase  Verification ](http://image.rman.top/blog20201114133609.png)

## Challenges for Graph Deep Hash:

* The threshold for graph similarity search may vary from query to query, it is appropriate to treat it as a regression problem, which requires estimating GED by hamming distance.
* How to establish a relationship between hamming distance and GED is a problem.
* Lack of positive samples.

## Contribution

* We provide a **first** attempt to use a neural-network-based approach to address the similarity search problem for graph databases via graph hashing.
* We propose a novel method GHashing, a **GNN-based** graph hashing approach, which can **automatically** learn a hash function that maps graphs into **binary vectors**, to enhance the **pruning-verification framework** by providing a fast and accurate pruner.
* We conduct extensive experiments to show that our method not only has an average F1-score of 0.80 on databases with **5** **million graphs** but also on average **20× faster** than the only state-of-the-art baseline on million-scale datasets.

## Preliminaries

•Graph Edit Distance (GED): $ged(g_1,g_2)$ is defined as the minimum number of edit operations to make $g_1$ isomorphic to $g_2$. The operation can be one of the following:

1. Insert a node
2. Delete a node
3. Change the label of the node
4. Insert an edge
5. Delete an edge

Graph Similarity Search under GED: Given a graph database $D$ with $N$ graphs, and a query graph $q$ and a threshold $τ$, the graph similarity search under GED measure aims to find ${g│g∈D∧ged(g,q)≤τ}$

## Approach:

### Hash function :

![Graph  Conv  Offline  Stage  Graph  Conv  Graph  Attention  Conv  Pooling  Graph Feature Extraction  Graph  Continuous  Embedding  Continuous  Binarization  Graph  Conv  Graph  Conv  Graph  Attenti on  Conv  Pooling ](http://image.rman.top/blog20201114135325.png)

#### Graph neural network:

![wEN (v)  h (1+1)  = (h(vl), m  (7)  (8) ](http://image.rman.top/blog20201114135327.png)

#### Graph pooling:

![IVI  (10)  IVI  n=l  where 0(•) is any activation function, V•V/a is trainable parameters  and ReLU(X) = max(X, 0). ](http://image.rman.top/blog20201114135330.png)

 

### Loss function:

$$
L=λL_{code} (g_1,g_2 )+(1-λ) L_{emb} (g_1,g_2 )
$$

$$
L_{code} (g_1,g_2 )=L(\hat y,y)
$$

$$
\hat {𝑦} =|𝐻(𝑔_1 )−𝐻(𝑔_2 )|,  𝑦=𝑔𝑒𝑑(𝑔_1,𝑔_2 ),  𝐿 can be any loss function for regression.
$$

#### Design of $L_{code}$

* GED can be arbitrarily large, the hamming distance between two B-bit hash codes can not be larger than B.
* Therefore, L should satisfy following properties:
  * Small punishment when both values are large $L(\hat y,y)=L′(min⁡{γ,\hat y},min{γ,y})$
  * Asymmetric: $L(\hat y,y)≠L(y,\hat y)$, estimated hamming distance should smaller than GED.
  * Minimized when $\hat y= y$
  * Convex

![image-20201114135804231](http://image.rman.top/blog20201114135804.png)

#### Handling discrete constraints

Hamming distance to Euclidean space

![image-20201114135859172](http://image.rman.top/blog20201114135859.png)

Binary regularization

![image-20201114135907457](http://image.rman.top/blog20201114135907.png)

#### **Design of** $L_{emb}$

![image-20201114135934082](http://image.rman.top/blog20201114135934.png)

### Online Stage

1. Encode the query graph to $H(q)$
2. Search the keys whose hamming distance to $H(q)$ is less than $τ+t$, $t$ is set to 1.

3. Second-stage pruning by computing$||F(g)-F(q)||_2^2>τ+0.5$

4. Verify every graph remained with exact verification algorithm

![Online  Stage  010  Hash Key  010  011  101  110  111  Hash Index  Pruning Phase  Fir-st-Level ](http://image.rman.top/blog20201114135956.png)

## Experiment:

### Datasets

![Dataset  AIDS  LINUX  ALCHEMY  IDI  42, 687  38, 661  119, 487  5, 000, 000  5, 000, 000  ave IVI  25  16  21  10  10  ave IEI  55  34  44  66  40  ILOI  62  Lel ](http://image.rman.top/blog20201114140100.png)

 

![Table 2: Recall/lcl ratio (x 10¯5) for GHashing and Naive  2  3  4  6  690  270  150  98  69  52  AIDS  GH  30  19  15  11  7.3  6.2  ALCHEMY  Naive  7.0  5.5  4.8  4.3  3.8  3.6  23  20  16  14  13  11  GH  15  12  10  8.8  7.9  6.7  N aive  16  12  10  8.6  7.7  6.6  11  4.9  2.8  1.9  1.2  GH-  3.5  2.4  1.7  1.4  1.0  1.0  Naive  4.2  2.7  1.9  1.4  1.2  1.0 ](http://image.rman.top/blog20201114140108.png)

 

![Table 3: Recall/lcl ratio for different code lengths on  AIDS dataset  24 bit  28  18  13  9.6  7.5  6.1  32 bit  19  15  11  7.1  48 bit  70  43  30  21  15  12 ](http://image.rman.top/blog20201114140111.png)

 

![(i)lCl vs.r(ALCHEMY)  冖 3 ) ICI vs.T(AIDS)  (j)Pvs.T(ALCHEMY)  冖 f)Pvs.TdlNUX)  (b) p vs. T(AIDS)  (k) R vs.T(ALCHEMY) (1) Fl vs.r(ALCHEMY)  (g) R vs. T(LINUX)  (c)Rvs 44m 匕  (h)F1vs r(LINUX)  (d)F1vs.r(AIDS) ](http://image.rman.top/blog20201114140114.png)

为什么$\tau$越大recall反而下降了呢？

在问了老师之后，可能的解释是随着$\tau$上升，正例的样本也变多了，所以recall下降了。

![Real GED  Figure 3: Violin plots of predicted GED given real GED on  AIDS dataset. A violin plot is an extension of the box plot,  where each "violin" is a plot of the density function of a his-  togram produced from the data. ](http://image.rman.top/blog20201114140118.png)

The results in Figure 3 show that the hamming distance is usually smaller than the real GED

![• BSS-GED  1E+2  • ML-ındex.._GH  E 1E+O  İZ 1E-I  1E-2  g 1E-3  ı. ı. ı. II ı II  • Inves  ı.E+3  11.E+2  •-I.E+I  21.E+0  ı.E+1  zı.E+O  ı.E-ı  ı.E-3  ı.E+2  12345  (a) T vs. T (AIDS)  ı.E+2  zı.E+1  El.E+O  ı.E-2  1 23  (d) R/T vs. T (AIDS)  6  6  12345  (b) T vs. T (LİNUX)  6  123456  (c) T vs. T (ALCHEMY)  ı.E+1  ZI.E+O  Z ı.E-ı  ı.E-2  11  123456  (e) R/T vs. T (LİNUX)  ı  2  3456  (f) R/T vs. T (ALCHEMY)  Figure 4: Average query time (T), recall/time ratio for GH  and three baselines on three real datasets. The truncated bar  means the total running time exceeds 1000 minutes. ](http://image.rman.top/blog20201114140300.png)

![Table 4: Costs of offline stage for GHashing and ML-Index  Dataset  AIDS ALCHEMY LNUX  NIL-I  n dex  Time  Space  Time  Space  759s  56M  696s  678M  443s  136M  248s  960M  1143s  52M  N/A  N/A  3172s  5.1G  N/A  N/A  3544s  5.1G  N/A  N/A ](http://image.rman.top/blog20201114140305.png)

在线查询快，recall高，offline训练时间少，存储空间少。好就完事了！yizhou Sun 牛！