---
date: 2023-11-24T06:28:40.000+08:00
aliases:
  - 映射
---

**映射（Mapping）** 一种将一个集合的元素转换到另一个集合的元素的规则。映射也被称为函数，它描述了元素之间的关系。

## 要素

一个映射通常由三个重要的要素组成：

1. 源集合（Domain）：这是映射的起始点，也就是映射的输入值可以来自的集合。我们用 $A$ 来表示源集合。
2. 目标集合（Codomain）：这是映射的终点，也就是映射的输出值所在的集合。我们用 $B$ 来表示目标集合。
3. 规则（Rule）：这是映射的定义，它描述了如何将源集合的元素映射到目标集合中的元素。我们用 $f$ 或者其他字母来表示映射的规则。

## 类型

### 单射（Injection）

如果每个不同的输入元素都被映射到不同的输出元素，那么映射是单射。也就是说，对于映射 $f: A \to B$，如果对于任意 $a_1, a_2 \in A$，当 $a_1 \neq a_2$ 时，$f(a_1) \neq f(a_2)$，则映射 $f$ 是一个单射。

^c2f49e

性质：

- 对于任意的 $a_1, a_2 \in A$，如果 $f(a_1) = f(a_2)$，则 $a_1 = a_2$。这意味着不同的输入元素永远映射到不同的输出元素。

### 满射（Surjection）

如果对于目标集合 $B$ 中的每个元素，都存在至少一个源集合 $A$ 中的元素与之对应，那么映射是满射。也就是说，对于映射 $f: A \to B$，如果对于每个 $b \in B$，存在至少一个 $a \in A$，使得 $f(a) = b$，则映射 $f$ 是一个满射。

### 满单射（Bijection）

如果一个映射既是单射又是满射，那么它是满单射。也就是说，对于映射 $f: A \to B$，如果对于任意的 $a_1, a_2 \in A$，当 $a_1 \neq a_2$ 时，$f(a_1) \neq f(a_2)$，并且对于每个 $b \in B$，存在唯一的 $a \in A$，使得 $f(a) = b$，则映射 $f$ 是一个满单射。

- 满单射将源集合 $A$ 中的每个不同的元素映射到目标集合 $B$ 中的不同元素，同时覆盖了整个目标集合。
- 满单射是一种一一对应的映射，每个元素在映射中有唯一对应的元素。

当两个结构之间存在一个满单射映射，并且这个映射保持了两个结构之间的运算和关系，我们就可以说这两个结构是 [[Isomorphism|同构]] 的。