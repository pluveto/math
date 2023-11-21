---
title: Bilinear Pairing
date: 2023-11-19T22:35:10.000+08:00
aliases:
  - 双线性配对
---

具体的来说，双线性映射定义了三个素数 $p$ 阶 [[Multiplicative Cyclic Group|乘法循环群]] $G_1$，$G_2$，和 $G_T$。并且定义在这三个群上的一个映射关系 $e: G_1\times G_2 \rightarrow G_T$，并且满足以下的性质：

1. 双线性：对于任意的 $g_1 \in G_1, g_2 \in G_2, a,b \in Z_p$，成立 $e(g_1^a,g_2^b)=e(g_1,g_2)^{ab}$ ；
2. 非退化性：$\exists g_1 \in G_1, g_2 \in G_2$ 满足 $e(g_1,g_2) \neq 1_{G_T}$。
3. 可计算性：存在有效的算法，对于 $\forall g_1 \in G_1, g_2 \in G_2$，均可计算 $e(g_1,g_2)$。

如果 $G_1 = G_2$ 则称上述双线性配对是对称的，否则是非对称的。

另外，上述的双线性配对是素数阶的，还存在一种合数阶的双线性配对，最早也是由 Boneh 等人在中引入密码学领域的。合数阶双线性配对利用子群的正交性可以在实现更加复杂的功能前提下完成安全性证明。[^1]

## 性质

除了上述三个性质，还可推出如下性质：

$$
\begin{aligned}
& e(A, B+C)=e(A, B) \cdot e(A, C) \\
& e(A+B, C)=e(A, C) \cdot e(B, C) \\
& e(n A, B)=e(A, n B)=e(A, B)^n
\end{aligned}
$$

[^1]: 什么叫双线性配对？<https://www.zhihu.com/question/39641890/answer/82350855>
