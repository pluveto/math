---
aliases:
- 公因数
---

如果一个整数同时是几个整数的因数，称这个整数为它们的“公因数”；公因数中最大的称为最大公因数。

---

**定理**：如果 $\text{gcd}(a,n)=1$ 且 $\text{gcd}(b,n)=1$，则 $\text{gcd}(a \cdot b, n)=1$ ^a4adcf

证明：

根据[[Bézout's Identity|贝祖定理]]，对于任意两个整数 $x$ 和 $y$，存在整数 $m$ 和 $n$ 使得它们的最大公约数 $gcd(x, y)$ 可表示为 $x \cdot m + y \cdot n$。

假设 $d = gcd(a \cdot b, n)$，那么存在整数 $m$ 和 $n_1$ 使得 $d = a \cdot b \cdot m + n \cdot n_1$。

-  $d = a \cdot b \cdot m + n \cdot n_1 = a\cdot bm+ n \cdot n_{1} = b \cdot am + n \cdot n_{1}$

我们可以看到，$d$ 是 $a \cdot b \cdot m$ 和 $n \cdot n_1$ 的线性组合，由于 $d$ 能整除 $a \cdot b$，$d$ 也必然能整除 $a$（或者 $b$）。