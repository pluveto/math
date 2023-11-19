---
date: 2023-11-19T15:10:47.000+08:00
---

## 引言

Why and How zk-SNARK Works: Definitive Explanation（[1906.07221.pdf](https://arxiv.org/pdf/1906.07221.pdf)）一文介绍了 zk-SNARK 的工作原理，讲解水平对新手很友好，不过我还是太菜，所以写这篇文章进行记录。本文基本上是对上面这篇文章的翻译和二次阐述。

## 开始

Prover 声称 知道 [[Polynomial|多项式]] $p(x)$ 的所有系数（下面简称 $\text{stmt}$），并且知道 $p(x)$ 的一些零点（这些零点可以告诉 Verifier）。

我们的问题是，**如何在不告诉 Verifier 关于多项式的知识（任何一个系数）的前提下，让 Verifier 相信 $\text{stmt}$ 为真**。

**如果** Prover 是对的，那么存在如下分解：

$$
p(x) = t(x) h(x)
$$

其中：

- $t(x) = (x - C_1)(x - C_2)\ldots(x - C_i)$ 称为**目标多项式（Target Polynomial）** 或者辅因子（cofactor）。
  - $C_0,\ C_1,\ \dots,\ C_i$ 是零点。
- $h(x)$ 是分解的剩余部分。

但是上面这个只是基于 $\text{stmt}$ 推出来的。为了证明 $\text{stmt}$，Prover 必须证明存在 $h(x)$ 使得此分解成立。

证明存在的最好方式就是算出来看看。那么 Prover 怎么算出来呢？Prover 可以用多项式除法算出来 $h(x) = \dfrac{p(x)}{t(x)}$。

> 注意：这里必须要求 $h(x)$ 算出来是整数，否则对于任意 $t(x)$，都能找到对应的 $h(x)$。既然可以胡编，那就无法相信 Prover。

而 Verifier 知道的信息只有这些：

1. $C_1, C_2, \ldots, C_i$ 以及衍生的 $t(x) = (x - C_1)(x - C_2)\ldots(x - C_i)$
2. 对于任意的 $r$，$p(r) = t(r) h(r)$ 应该成立。

因此设计如下协议：

1. Verifier 随机给出一个值 $r$，告诉 Prover。
2. Prover 计算 $p(r)$、$h(r)$，并发送给 Verifier。
3. Verifier 检查 $t(r) h(r)$ 是否等于 $p(r)$。
    - 如果总是等于 $p(r)$，Verifier 认为 Prover 的声称 $\text{stmt}$ 是真的。
    - 否则拒绝。

我的理解是，通过因式分解，Verifier 知道了多项式的一部分因式，或者说辅因子 $t(x)$，这有几个好处：

1. Verifier 并不能根据 $t(x)$ 得到任何一个系数的值。
2. 同时又让 Verifier 能根据 $t(x)$ 检查 $p(r)$, $h(r)$ 是否满足约束。
3. **如果 Prover 其实不知道 $p(x)$，那么大概率算出来的 $h(r)$ 会产生余项。**

一旦产生余项，就知道 Prover 说谎了。可还是有可能，即便说谎，也发生**余项消除**，因为余项的形式是多项式相除（例如 $\dfrac{{7x-6}}{t(x)}$），这种情况下协议失效。

除了余项消除导致协议失效，还可以这样：

- Prover 根据 $r$ 计算出 $t(r)$，然后随便找一个随机多项式 $h(x)$，代入 $r$ 得到 $h(r)$，再让 $h(r)$ 和 $t(r)$ 想成，得到 $p(r)$，这样能伪造的同时，不会产生余项。

## 同态加密

[[Homomorphic Encryption|同态加密]] 可以利用 [[Homomorphism|同态]] 的性质，在不暴露原文的情况下进行一些运算。尤其利用模运算的 [[Modular Arithmetic|同余]] 性质，可以形成 [[Finite Field|有限域]]。
