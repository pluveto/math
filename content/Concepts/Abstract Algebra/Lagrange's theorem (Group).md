---
date: 2023-11-23T08:30:21.000+08:00
aliases:
  - 拉格朗日定理
---

拉格朗日定理是群论中的一个基本定理，它给出了[[Finite Group|有限群]]的子群的阶（即元素的个数）与整个群的阶之间的关系。

具体地说，假设 $G$ 是一个有限群，$H$ 是 $G$ 的一个子群。那么 $H$ 的阶（记作 $|H|$）是 $G$ 的阶（记作 $|G|$）的一个因子。换句话说，$|G|$ 是 $|H|$ 的整数倍。

拉格朗日定理的证明通常依赖于[[Left Coset|左陪集]]的概念。给定群 $G$ 中的一个子群 $H$ 和 $G$ 中的任意元素 $g$，可以构造 $g$ 和 $H$ 的左陪集 $gH = \{gh : h \in H\}$。所有这样的左陪集将 $G$ 划分为互不相交的等价类，每个等价类的大小都等于 $H$ 的阶。

证明拉格朗日定理的关键步骤如下：

1. 左陪集的等价关系：首先证明左陪集划分构成了一个等价关系。这意味着每个元素 $g \in G$ 都属于某个左陪集，并且任意两个左陪集要么完全相同，要么完全不相交。
2. 左陪集的大小：接着证明任意两个左陪集的大小相同，且这个大小等于子群 $H$ 的阶。这是因为存在从 $H$ 到任意左陪集 $gH$ 的一个双射，即对于 $H$ 中的每个元素 $h$，都有 $gH$ 中的唯一元素 $gh$ 与之对应。
3. 计数原理：最后，由于 $G$ 是有限的，我们可以计算 $G$ 的阶，即 $G$ 中元素的总数。由于 $G$ 被划分成了大小相等的左陪集，$G$ 的阶 $|G|$ 等于左陪集的个数乘以子群的阶 $|H|$。这意味着 $|H|$ 必须是 $|G|$ 的一个因数。

通过上述步骤，我们可以看到，任何群 $G$ 的子群 $H$ 的阶 $|H|$ 必然整除群 $G$ 的阶 $|G|$，这就是拉格朗日定理的内容。