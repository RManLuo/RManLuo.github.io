---
title: torch.einsum 实现CIN
date: 2020-05-09 12:24:09
tags:
	- pytorc
	- python
categories:
	- python
cover:
mathjax: true
---

研究了一下如何用pytorch实现CIN的操作。

## CIN的数学原理

假设总共有$m$个field，每个field的embedding是一个$D$维向量。

压缩交互网络（Compressed Interaction Network， 简称CIN）隐向量是一个单元对象，因此我们将输入的原特征和神经网络中的隐层都分别组织成一个矩阵，记为$X_0$和$Xk$，CIN中每一层的神经元都是根据**前一层的隐层**以及**原特征向量**推算而来，其计算公式如下：
$$
X^k_{h,*}=\sum ^{H_{k-1}}_{i=1}\sum ^{m}_{j=1}W^{k,h}_{ij}(X^{k-1}_{i,*}\circ X^{0}_{j,*})
$$



其中，第k层隐层含有$H_k$条神经元向量。$\circ$是Hadamard product，即element-wise product，即，$ \left \langle a_1,a_2,a_3\right \rangle\circ \left \langle b_1,b_2,b_3\right \rangle=\left \langle a_1b_1,a_2b_2,a_3b_3 \right \rangle $.

根据前一层隐层的状态$X^k$和原特征矩阵$X^0$，计算出一个中间结果$Z^{k+1}$，它是一个三维的张量。注意图中的$\bigotimes$是outer product，其实就是矩阵乘法咯，也就是一个mx1和一个nx1的向量的外积是一个mxn的矩阵：
$$
u\bigotimes v=uv^T=\begin{bmatrix}
u_1\\ 
u_2\\ 
u_3\\
u_4
\end{bmatrix}\begin{bmatrix}
v_1 & v_2  & v_3  
\end{bmatrix}=\begin{bmatrix}
u_1v_1 &  u_1v_2& u_1v_3 \\ 
u_2v_1 & u_2v_2 & u_2v_3\\ 
u_3v_1 & u_3v_2 & u_3v_3\\ 
u_4v_1 & u_4v_2 & u_4v_3
\end{bmatrix}
$$




![img](http://image.rman.top/20200509123153.png)

而图中的$D$维，其实就是左边的一行和右边的一行对应相乘。

https://daiwk.github.io/posts/dl-dl-ctr-models.html#cin

## pytorch的实现

在pytorch的实现过程中主要使用的是`torch.einsum`操作

https://pytorch.org/docs/stable/torch.html?highlight=einsum#torch.einsum

> `torch.``einsum`(*equation*, **operands*) → Tensor[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/functional.html#einsum)
>
> This function provides a way of computing multilinear expressions (i.e. sums of products) using the Einstein summation convention.
>
> - Parameters
>
>   **equation** (*string*) – The equation is given in terms of lower case letters (indices) to be associated with each dimension of the operands and result. The left hand side lists the operands dimensions, separated by commas. There should be one index letter per tensor dimension. The right hand side follows after -> and gives the indices for the output. If the -> and right hand side are omitted, it implicitly defined as the alphabetically sorted list of all indices appearing exactly once in the left hand side. The indices not apprearing in the output are summed over after multiplying the operands entries. If an index appears several times for the same operand, a diagonal is taken. Ellipses … represent a fixed number of dimensions. If the right hand side is inferred, the ellipsis dimensions are at the beginning of the output.**operands** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)) – The operands to compute the Einstein sum of.

```python
print(a_tensor)
 
tensor([[11, 12, 13, 14],
        [21, 22, 23, 24],
        [31, 32, 33, 34],
        [41, 42, 43, 44]])
 
print(b_tensor)
 
tensor([[1, 1, 1, 1],
        [2, 2, 2, 2],
        [3, 3, 3, 3],
        [4, 4, 4, 4]])
 
# 'ik, kj -> ij'语义解释如下：
# 输入a_tensor: 2维数组，下标为ik,
# 输入b_tensor: 2维数组，下标为kj,
# 输出output：2维数组，下标为ij。
# 隐含语义：输入a,b下标中相同的k，是求和的下标，对应上面的例子2的公式
output = torch.einsum('ik, kj -> ij', a_tensor, b_tensor)
 
print(output)
 
tensor([[130, 130, 130, 130],
        [230, 230, 230, 230],
        [330, 330, 330, 330],
        [430, 430, 430, 430]])
```

简而言之，可以用`ik, kj`来指代输入矩阵的各个维度，然后用`->`来定义行列的操作。

### CIN实现

``` python
a=torch.randn(2,30,8) # (Batch size, feature number, feature dim)
b=torch.randn(2,30,8) # (Batch size, feature number, feature dim)
c=torch.einsum('bhd,bmd->bhmd',a,b) # b是batch size， h,m是各自的特征数量，d是特征纬度
c.size()
# torch.Size([2, 31, 31, 8])
```

