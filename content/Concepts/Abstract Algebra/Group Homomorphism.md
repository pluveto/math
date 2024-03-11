---
date: 2023-11-24T06:42:48.000+08:00
aliases:
  - 群同态
---

**群同态（Group Homomorphism）** 是一种特殊的映射关系，它保持了群之间的运算结构和单位元素。

## 定义

设有两个群 $(G, \cdot)$ 和 $(H, \circ)$，其中 $G$ 和 $H$ 是群的集合，$\cdot$ 和 $\circ$ 分别是群 $G$ 和 $H$ 的运算。一个群同态是一个映射 $f: G \to H$，满足以下两个条件：

1. 对于任意的 $a, b \in G$，有 $f(a \cdot b) = f(a) \circ f(b)$，即对于群 $G$ 中的元素，它们的映射到群 $H$ 中后的运算结果等于它们的映射分别运算后的结果。（[[Homomorphism|同态]]）
2. 对于群 $G$ 的单位元素 $e_G$，有 $f(e_G) = e_H$，即群 $G$ 的单位元素映射到群 $H$ 的单位元素。

## 例子

举例来说，考虑两个整数加法群 $(\mathbb{Z}, +)$ 和 $(\mathbb{Z}_2, +)$，其中 $\mathbb{Z}$ 是整数集合，$\mathbb{Z}_2$ 是模 2 整数集合。我们定义一个映射 $f: \mathbb{Z} \to \mathbb{Z}_2$，其中 $f(n)$ 表示整数 $n$ 模 2 的余数。

例如，对于 $n = 3$，我们有 $f(3) = 1$，因为 3 除以 2 的余数是 1。同样地，对于 $n = -2$，我们有 $f(-2) = 0$，因为 -2 除以 2 的余数是 0。

这个映射 $f$ 是一个群同态，因为它满足群同态的两个条件。首先，对于任意的整数 $a, b$，我们有 $f(a+b) = (a+b) \mod 2 = (a \mod 2 + b \mod 2) \mod 2 = f(a) + f(b)$。其次，单位元素 0 映射为 $\mathbb{Z}_2$ 的单位元素 0。
