---
date: 2023-11-25T15:30:19.000+08:00
---

扩展欧几里得算法（Extended Euclidean Algorithm）是欧几里得算法的扩展版本，用于计算两个整数的最大公约数（GCD）以及一对整数的 [[Bézout's Identity|贝祖等式]]（Bézout's identity）中的系数。

它的基本原理是通过重复应用辗转相除法，将较大的数除以较小的数，然后用余数替换较大的数，直到余数为零。最后的非零余数就是两个整数的最大公约数。

扩展欧几里得算法除了计算最大公约数外，还能找到一对整数的贝祖等式的系数。贝祖等式是指对于给定的整数 a 和 b，存在整数 $x$ 和 $y$，使得 $ax + by = gcd(a, b)$。扩展欧几里得算法通过在欧几里得算法的每一步中保持贝祖等式的成立，计算出 x 和 y 的值。

---

**例子**：使用 EEA 找到任意元素的乘法逆元。

#card <!--2023/11/25/DiDErHA-->

分析：

我们需要找到一个数 $a^{-1}$，使得 $(a \cdot a^{-1}) \mod p = 1$，这可以被改写为：$a \cdot a^{-1} = p \cdot k + 1$，其中 $k$ 是一个整数。这就是一个贝祖等式，我们可以用扩展欧几里得算法来求解 $a^{-1}$。

```python
def extended_gcd(a, b):
    if a == 0:
        return b, 0, 1
    else:
        gcd, x, y = extended_gcd(b % a, a)
        return gcd, y - (b // a) * x, x

def mod_inverse(a, p):
    gcd, x, y = extended_gcd(a, p)
    return x % p
```

在这个代码中，`mod_inverse(a, p)` 函数会返回 $a$ 在模 $p$ 下的乘法逆元。

---
