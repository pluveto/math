---
title: Homomorphic Encryption
date: 2023-11-19T16:24:12.000+08:00
aliases:
- 同态加密
---

## 概念

同态加密（Homomorphic Encryption，HE）：一种基于 [[Homomorphism|同态]] 的加密形式，允许对已加密数据执行计算，而无需先解密。生成的计算以加密形式保留，解密后，输出与**对未加密数据执行操作时的**输出相同。

下面的伪代码可解释此概念：

```ts
let he = createEncryptor(generateKey())

let num1 = 10
let num2 = 20

let cipherNum1 = he.encrypt(num1) 
let cipherNum2 = he.encrypt(num2)
let cipherSum  = he.addOperation(cipherNum1, cipherNum2) 
let plainSum   = he.decrypt(cipherSum)

assert(plainSum === num1 + num2) // 打印 30,与明文相加结果相同
```

## 例子

举个例子，幂函数对于加法乘法运算构成一种典型的同态。我们使用 $5$ 的幂函数实现同态加密。首先，选择两个密文消息 $a$ 和 $b$，它们都是 $5$ 的幂，即 $a = 5^x$，$b = 5^y$。

密文消息相加对应其实数值乘法：

$c = a + b = 5^x + 5^y = 5^{x+y}$

将结果 $c$ 还原为 $5$ 的幂，就得到了明文和：

$\text{plaintext} = x + y$

可以代入一些具体数据加深理解：

- 明文消息 $a = 2$，$b = 3$
- 密文 $a = 5^2 = 25$、密文 $b = 5^3 = 125$
- 密文乘积相当于加法 $c = 25 \cdot 125 = 3125 = 5^5$
- 明文和 $= 2 + 3 = 5$

可以看到，通过 $5$ 的幂操作实现了同态加密的基本属性：

1. 密文间的加法对应明文间的乘法。
2. 解密后的结果与直接在明文上计算相同。

- [!] 这只是一个极简化的同态加密模型，实际的同态加密算法会更复杂。

## 运算

目前，同态加密支持的运算主要为加法运算和乘法运算。按照其支持的运算程度，同态机密分为半同态加密和全同态加密：

- **半同态加密（Partially Homomorphic Encryption, PHE）** 在数据加密后只持加法运算或乘法运算中的一种。根据其支持的运算的不同，又称为加法同态加密或乘法同态加密。半同态加密由于机制相对简单，相对于全同态加密技术，拥有着更好的性能。
- **全同态加密（Fully Homomorphic Encryption, FHE）** 对加密后的数据支持任意次数的加法和乘法运算。

典型的同态加密算法包括 Paillier 算法。
