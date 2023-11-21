---
title: zk-SNARK 浅显分析
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

- $t(x) = (x - C_1)(x - C_2)\ldots(x - C_i)$ 称为辅因子（cofactor）。
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

对于之前的多项式 $p(x) = k_0 + k_1 x + k_2 x^2 + \ldots + k_n x^n$，可以将其映射为一个**加密多项式** $(E(x^0))^{k_0} \cdot (E(x^1))^{k_1} \cdot (E(x^2))^{c_k} \cdot \ldots \cdot (E(x^n))^{k_n}$，记作 $g^{p(x)}$。

其中，$E(x) = g^x$，$g$ 是底数，$x$ 是指数。

于是我们得到改进版协议，修复暴露原文 $r$ 的问题。协议如下：

1. Verifier 取秘密随机数 $s$:
    - 先计算未加密的 $t(x)$ 辅因子多项式，即得到数值 $t(s)$。暂存。
    - 将 $s$ 代入 $E(x)$，得到各次幂的加密形式 $E(s^i) = g^{s_i}$。
    - 将这一列加密形式 $g^{s_i}$ 直接发送给 Prover。
2. Prover 根据 $g^{s_i}$ 序列，计算出 $g^{p(s)}$ 和 $g^{h(s)}$，并发送给 Verifier。
3. Verifier 最后检查。它**原本应该**检查 $t(s) h(s)$ 是否等于 $p(s)$。但已加密。我们看看怎么办。
	- 现在有什么？
		- $t(s)$ 是知道的。
		- $h(s)$ 现在不知道。只知道 $g^{h(s)} = g^{h(s)}$ 是 Prover 给出的。
		- $p(s)$ 现在不知道。只知道 $g^{p(s)} = g^{p(s)}$ 是 Prover 给出的。
	- 为了凑出 $t(s) h(s) = p(s)$，Verifier 只需要计算 $\text{unk} \cdot g^{h(s)} = g^{p(s)}$ 即可。其中 $\text{unk}$ 是未知数。
	- $\text{unk} = \dfrac{g^{p(s)}}{g^{h(s)}} = g^{p(s) - h(s)}$，而 $p(s) - h(s)$ 实际上不知道。
	- 但不需要知道，因为可进行如下推导：
		- 如果满足 $t(s) h(s) = p(s)$，那么 $g^{h(s) \cdot t(s)} = g^{p(s)} = g^{p(s)}$ 应该成立
		- 从而，**问题转换为验证 $(g^{h(s)}))^{t(s)} = g^{p(s)}$ 是否成立**。这里 $g^{h(s)}$、$g^{p(s)}$ 都从 Prover 接收到，$t(s)$ 是我们的秘密值。

所以我们只要用秘密值 $t(s)$ 对 $g^{h(s)}$ 进行幂运算，用 $(g^{h(s)}))$ 和 $g^{p(s)}$ 比较是否相等即可验证。

下面考察 Prover 能否作弊：

文章中说：

> While in such protocol the prover’s agility is limited he still can use any other means to forge a proof without actually using the provided encryptions of powers of s, for example, if the prover claims to have a satisfactory polynomial using only 2 powers $s^3$ and $s^1$ , that is not possible to verify in the current protocol.

这里说实话，我不理解。如果读者看懂了，欢迎指教。

如果 Verifier 只验证 $(g^{h(s)})^{t(s)} = g^{p(s)}$ 是否成立，那么只要构造出符合要求的伪造值 $z_h$ 和 $z_p$，满足 $z_p = z_h^{t(s)}$，就可以通过验证。伪造能够通过的原因是，这些伪造值不是通过 $s$ 的加密值 $E(s)$ 算出的，而是 Prover 自己编的。要是能解决这个问题就好了。

通过 [[Knowledge-of-Exponent Assumption|KEA]] 方法可以解决这个难题，首先需要理解 [[Knowledge-of-Exponent Assumption#例子|这个例子]]

利用这个原理，可以限制 Prover 只能用 Verifier 提供的加密的 $s$ 进行计算，因而 Prover 只能将系数 $c$ 赋给 Verifier 提供的多项式。

1. Verifier 和 Prover 约定加密底数 $g$。
2. Verifier 随机采样一个值 $s$，但这个值保密，永远不会告诉 Prover。
3. Verifier 计算 $s$ 的各次幂加密值：$g^{(s^0)}$, $g^{(s^1)}$, $g^{(s^2)}$, ..., $g^{(s^n)}$。并把这些值发送给 Prover。
4. Verifier 计算 $s$ 的各次幂加密值经过 KEA 的变换 (shift) 后的值：$g^{(\alpha s^0)}$, $g^{(\alpha s^1)}$, $g^{(\alpha s^2)}$, ..., $g^{(\alpha s^n)}$。并把这些值发送给 Prover。
	- 这一步，具体来说就是简单地随便找个 $\alpha$，分别乘以 $g^\alpha$.
5. Prover 基于 $g^{(s^0)}$, $g^{(s^1)}$, $g^{(s^2)}$, ..., $g^{(s^n)}$ 计算出加密的多项式 $g^p = g^{(p(s))}=$$g^{(s^0)c_{0}}\cdot g^{(s^1)c_{1}}\cdot g^{(s^2)}$, ..., $g^{(s^n)c_{n}} = g^{c_{0}s^0+c_{1}s^1+\cdot +c_{n}s^n}$。发送给 Verifier。
6. Prover 基于 $g^{(\alpha s^0)}$, $g^{(\alpha s^1)}$, $g^{(\alpha s^2)}$, ..., $g^{(\alpha s^n)}$ 计算出加密且变换后的多项式 $g^{\alpha{(c_{0}s^0+c_{1}s^1+\cdot +c_{n}s^n})}$，发送给 Verifier。
7. Verifier 检查是否成立 $(g^p)^\alpha =g^{\alpha{(c_{0}s^0+c_{1}s^1+\cdot +c_{n}s^n})}$
	- LHS 来自 Prover 发来的第一个多项式 $g^p$
	- RHS 来自 Prover fall 的第二个加密 +Shift 后的多项式，我们也叫做 $g^{p^\prime}$
	- $\alpha$ 只有 Verifier 自己知道。

问题：

1. 由于 $c_{i}$ 的取值范围有限，存在爆破的可能。
2. 证明过程是交互式的，这对于 Verifier 没问题，但对于需要更多参与者的系统而言，无法信任 Verifier 没有和 Prover 串通。

## 非交互式

为了实现非交互式证明，需要一个可以重用、公开、可信、不可滥用的秘密参数。

安全性的主要障碍在于 $t(s)$ 和 $\alpha$ 两个值的泄露。因此可以使用同态加密。但要加密的对象还包括 $t(s) h(s)$（回顾：之前提过，要验证 $(g^{h(s)}))^{t(s)} = g^{p(s)}$ 是否成立）。之前的同态加密把乘法映射为了加法，因此在加密后的数据上不再能够使用乘法。所以类似这样的加密过的乘积，无法再次加密。

因此引入叫做 [[Bilinear Pairing|双线性映射]] 的密码学配对方法。双线性映射表示为函数 $e(g,g)$，给定两个加密的输入（即 $g_{a}, g_{b}$），可以将它们确定性地映射到另一组不同的输出数据集上的它们的乘积，即 $e(g_{a}, g{b}) = e(g, g)^{ab}$。

注意：

- 一个配对的结果不能用做其他配对计算的输入。
- 一次配对只能将**两个**加密值相乘。

## Setup

原始数据 $g^\alpha, g^{s^i}, g^{\alpha s^i}, g^{t(s)}$ 被称为 [[Common Reference String|通用引用字符串]]，简言之可以被各方读取。

- $(g^{s^i}, g^{\alpha s^i})$ 称为 Proving Key 或 Evaluation Key
- $(g^\alpha,g^{t(s)})$ 称为 Verification Key

来自 Prover 的数据则包括 $g^p, g^h, g^{p^\prime}$ 。因此最后的校验过程变成：

- 在加密空间校验 $p(s) = t(s)\cdot h(s)$，等价于：
	$e(g^p, g^1) = e(g^t, g^h)$
- 校验多项式的限制
  $e(g^p, g^\alpha) = e( g^{p^\prime}, g)$

CRS 用户必须相信生成者删除了 $\alpha$ 和 $s$. 一旦任何一方保留了参数，就会对整个系统造成威胁。

解决方法是使用组合后的 CRS：

- 假设有三个参与者 A、B、C
- A 生成它的随机数 $s_{A}, \alpha_{A}$ 计算并公开它的 CRS：$g^\alpha_{A}, g^{s^i}_{A}, g^{\alpha _{A}s^i}_{A}$
- B 生成它的随机数和 CRS 使用同态乘法 CRS 乘以 A 的 CRS 得到组合后的 CRS
- C 生成它的随机数和 CRS 使用同态乘法乘以 A、B 的组合 CRS

通过这个迭代过程，必须所有人串谋才能得到混合的 $s^i$ 和 $\alpha$

这样依然存在一个漏洞：一个恶意参与者并不一定会遵从前面的参与者的 CRS，而是直接抛弃，转而随机采样多个不同的 $s_1$、$s_2$ 等，以及 $\alpha_1$、$\alpha_2$ 等，并且随机地为不同的 $s$ 的幂（或提供随机数作为增强后的公共参考字符串）然后提供给下一个人，这会使 CRS 无效和无法使用。

即：如何确保每个参数都源于前一个

解决方法是用 $s^1$ 次为标标准，检查其他次幂的值与之是否保持一致。主要是利用了性质：

$e(g^{s^i}, g) = e(g^{s^1}, g^{i-1}) | i_{\in 2, \cdots \cdots, d}$

因此对于2次幂可以转换为：  
$e(g^{s^2}, g) = e(g^{s^1}, g^{s^1})$  $\Rightarrow e(g,g)^{s^2} = e(g,g)^{s^{1+1}}$

3次幂: 可以转换为：
$e(g^{s^3}, g) = e(g^{s^1}, g^{8^2})$  $\Rightarrow e(g,g)^{s^3} = e(g,g)^{s^{1+2}}$ 等。

这样还存在一个问题，最后一个人未必会遵守规则。因此除了第一个人之外，每个人需要公开它加密后的参数。

## zk-SNARK

我们用 $\{ s^i \}_{i \in [d]}$ 表示 $s^0,\ s^1,\ \dots,\ s^d$，并且 $t(x)$ 和 Prover 多项式阶数 $d$ 已知。

经过演化得到的 zk-SNARK 协议思路如下：

.Setup
挑选随机值 $s,a$
计算加密值 $g^x$ 和 $g^{s^i}$，$i \in [d]$, $g^{xsi}$，$i \in \{0,\cdots,d\}$
生成 proving key: ($g^{s^i}$)，$i \in [d]$, ($g^{xs^i}$)，$i \in \{0,\cdots,d\}$
生成 verification key: ($g^a$, $g^{t(s)}$)

.Proving
分配系数 {$c_i$}，$i \in \{0,\cdots,d\}$ (即知识)，得到多项式 $p(x) = c_d x^d + \cdots + c_1 x^1 + c_0 x^0$
求多项式 $h(x) = p(x)/t(x)$
代入 {$g^{s^i}$}，$i \in [d]$，计算多项式 $g^{p(s)}$ 和 $g^{h(s)}$ 的值
代入 {$g^{\alpha s^i}$}，$i \in [d]$，计算变换多项式 $g^{\alpha p(s)}$ 的值
选择随机数 $\delta$
构造随机化的证明 $\pi = (g^{6p(s)}, g^{8h(s)}, g^{8\alpha p(s)})$

.Verification
(映射证明 $\pi$ 为 ($g^p$, $g^h$, $g^{p'})$)
验证多项式约束：$e(g^p,g) = e(g^p,g^a)$
验证多项式系数：$e(g^p,g) = e(g^{t(s)},g^h)$

zk-SNARK 依然有局限性，它虽然证明了 Prover 的确知道一个多项式 $p(x)$，但不能证明 Prover 在协议中真正使用的是 $p(x)$，它还是可能隐藏真正的多项式，然后用 $t(x)$ 乘以一个有界多项式，伪装成 $p(x)$，并没有机制约束他不做这样的事情。

## 计算

上面实现了基于多项式的零知识方案，但实际应用中传输的数据不一定是多项式。

例如：

```lua
function calc(w, a, b)       
    if w then      
        return a × b       
    else       
        return a + b       
    end if      
end function
```

不过我们发现，它可以转换为如下形式：$f(w,a,b) = w(a \cdot b)+(1-w)(a+b)$

由于任何指令都可以转换成 $f(\text{opr}, \text{op\_left}, \text{op\_right})$ 的形式，我们就可以把任何程序转换成多项式的序列。

二元运算多项式

$$
l(x)\ \text{op}\ r(x)=o(x)
$$
如果成立，必然存在根 $a$ 使得 $l(a) \ \text{op}\ r(a) = o(a)$ 成立，则可得到目标多项式 $t(x) = x- a$.

此处 $t(x)$ 和前文提到的含义相同。

例如运算 $3 \times 2 = 6$ ，可以表示为多项式 $l(x) = 3x, r(x) = 6x, o(x) = 6(x)$ 在 $a = 1$ 时的值。

这个运算的多项式为 $l(x) r(x) = o(x)$, i.e. $3x \times 2x = 6x$ i.e. $6x^2 - 6x = 0 = (6x)(x-1)$

We could define $p(x) = l(x) \times r(x) - o(x) = t(x)h(x)$

So $l(x)r(x) = t(x)h(x)+o(x)$

https://hal.science/hal-03047093v1/file/homomorphic_journal%5B1%5D.pdf