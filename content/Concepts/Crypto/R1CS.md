R1CS（Rank-1 Constraint System）是一种用于构建零知识证明的系统，它可以用于证明一个人知道满足一组多项式等式的解，而不必透露任何关于解的信息[[1]](https://www.odaily.news/post/5188318)。传统的R1CS系统需要在证明过程中使用大量的随机性，这会导致证明的生成和验证过程非常复杂和耗时。

R1CS的转换过程通常包括以下几个步骤：

1. Flattening：将计算问题的代码转换为一系列基本操作，如赋值和算术运算[[2]](https://snowolf0620.xyz/index.php/zkp/435.html)。
2. R1CS转换：将Flattening的结果转化为一阶约束系统R1CS，其中R1CS由三个向量构成的向量组$(\vec a, \vec b, \vec c)$组成。解向量$\vec s$满足约束条件$(\vec s \cdot \vec a) \cdot (\vec s \cdot \vec b) - (\vec s \cdot \vec c) = 0$[[2]](https://snowolf0620.xyz/index.php/zkp/435.html)。
3. 零知识证明系统构建：基于R1CS的约束条件和解向量，构建一个实际的零知识证明系统，使得证明者可以证明自己知道满足约束条件的解，而不需要透露解的具体信息[[2]](https://snowolf0620.xyz/index.php/zkp/435.html)。

R1CS的应用非常广泛，特别是在零知识证明和密码学领域。它可以用于构建各种零知识证明系统，如zk-SNARKs（零知识可验证的非交互式证明系统），用于验证计算的正确性而不泄露敏感信息[[2]](https://snowolf0620.xyz/index.php/zkp/435.html)。
# Rank-1 Constraint Systems

## Notation

In order to explain how zk-SNARKs work, we'll borrow notation common in the Bulletproofs literature, as it may be familiar with readers.

* We use uppercase letters like $A$, $B$, and $C$ to describe elements of a group $\mathbb{G}$ which is of prime order $q$. In practice these will be elements of either $\mathbb{G}_1$ or $\mathbb{G}_2$.
* We use lowercase letters like $a$, $b$ and $c$ to describe scalars — elements of $\mathbb{F}_q$.
* We use boldface to describe vectors: $\textbf{a}$ is a vector of scalars and $\textbf{A}$ is a vector of group elements. We use $\textbf{p(x)}$ to describe vectors of polynomials with indeterminate $x$. We use $\textbf{x}^n$ to describe vectors of scalars that are sequences of powers of some $x \in \mathbb{F}_q$.
* We use the notation $\langle \textbf{a}, \textbf{b} \rangle$ to describe the inner product of two vectors — in other words, the elements of each vector are multiplied pairwise, and the products are summed together. Note that $\langle \textbf{a}, \textbf{b} \rangle$ produces a scalar and $\langle \textbf{a}, \textbf{B} \rangle$ produces a group element.

## R1CS

zk-SNARKs are zero-knowledge proofs which allow us to prove that we performed a computation $f(p, w)$ over some witness $w$ without revealing that witness. We express our computations in the form of arithmetic constraint systems.

Given an assignment $\textbf{z}$ of variables in $\mathbb{F}_q$, a **rank-1 constraint system** is a system of quadratic constraints of the form $a \cdot b = c$, where $a, b, c$ are linear combinations of our variable assignment. If we always set $z_0 = 1$, then these constraint systems can express any bounded computation.

* We can **boolean constrain** a variable $z_i$ with the constraint $(z_i) \cdot (z_i) = (z_i)$, because squaring is the identity for only $0$ and $1$ in a field.
* Given a boolean constrained variable $z_i$, we can express $\neg z_i$ as $z_0 - z_i$.
* Given two boolean constrained variables $z_i, z_j$, we can express $z_i \land z_j$ as $z_i \cdot z_j$.

As an example, consisider the constraint system:

1. $(z_1) \cdot (z_1) = (z_1)$
2. $(z_2) \cdot (z_2) = (z_2)$
3. $(2z_1) \cdot (z_2) = (z_1 + z_2 - z_3)$

The first two constraints are boolean constraints over $z_1$ and $z_2$, respectively. The third constraint is actually an XOR constraint: you'll see through substitution that $z_3$ *must* equal $z_1 \oplus z_2$.

All of the terms of the constraint system are linear combinations of every variable; in other words, the above constraint system can be equivalently expressed with a zero coefficient for every omitted variable:

* $(1z_1 + 0z_2 + 0z_3) \cdot (1z_1 + 0z_2 + 0z_3) = (1z_1 + 0z_2 + 0z_3)$
* $(0z_1 + 1z_2 + 0z_3) \cdot (0z_1 + 1z_2 + 0z_3) = (0z_1 + 1z_2 + 0z_3)$
* $(2z_1 + 0z_2 + 0z_3) \cdot (0z_1 + 1z_2 + 0z_3) = (1z_1 + 1z_2 + -1z_3)$

Let's begin to describe our constraint system using the inner product notation and coefficients represented by fixed vectors $\textbf{a}, \textbf{b}, \textbf{c}$:

* $\langle \textbf{a}_0, \textbf{z} \rangle \cdot \langle \textbf{b}_0, \textbf{z} \rangle = \langle \textbf{c}_0, \textbf{z} \rangle$
* $\langle \textbf{a}_1, \textbf{z} \rangle \cdot \langle \textbf{b}_1, \textbf{z} \rangle = \langle \textbf{c}_1, \textbf{z} \rangle$
* $\langle \textbf{a}_2, \textbf{z} \rangle \cdot \langle \textbf{b}_2, \textbf{z} \rangle = \langle \textbf{c}_2, \textbf{z} \rangle$

More generally, our goal is to demonstrate that we know a satisfying assignment $\textbf{z} = (1, \textbf{p}, \textbf{w})$ for which $\langle \textbf{a}_i, \textbf{z} \rangle \cdot \langle \textbf{b}_i, \textbf{z} \rangle = \langle \textbf{c}_i, \textbf{z} \rangle$ holds for all $i$ given fixed coefficients $\textbf{a}, \textbf{b}, \textbf{c}$. If we can do this without revealing $\textbf{w}$, and non-interactively with succinct proofs, we'll have a zk-SNARK.