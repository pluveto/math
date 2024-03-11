
下载并安装 [Electrum Bitcoin Wallet](https://electrum.org/#download)

首先看到的是创建/恢复钱包功能。

![image](https://raw.githubusercontent.com/pluveto/0images/master/2024/20240309102500-D4BE3A84-646E-4AFA-87DC-D9AEED5B1FF5.png)

我们生成一个助记词：

```
absent allow honey unfold call clutch pizza girl awful maze powder early
```

这个助记词仅用于本次测试。
## UI 功能分析

首先把语言调成英文，这样能和代码对应。对整个 UI 的分析如下：

![image](https://raw.githubusercontent.com/pluveto/0images/master/2024/electrum-ui.svg)

注意到 Network 中可以 取消勾选 Select server automatically. 我们起一个本地 btc 节点看看。

```
bitcoind -regtest

```

```
bitcoin-cli -regtest createwallet "wallet_demo"
bitcoin-cli -regtest -generate 101
bitcoin-cli -regtest sendtoaddress "bitcoin_address" 1.0
bitcoin-cli -regtest listaddresses

```