# 7. 氦气加密货币项目

在本章中，我们将开始构建氦气（Helium）加密货币。我们的实现将包含你对一个生产级加密货币所期望的所有特性。氦气模仿了作为权威标准的比特币。我们将用 Python 实现氦气。Python 是一种简单而强大的语言，其语法清晰且极其易于理解。对于清晰地揭示构成加密货币核心的算法和技术，Python 是一种理想的语言。使用 Python 的缺点在于它是一种解释型语言，因此天生缓慢。这一限制使其不适合生产环境。

如果你有兴趣，氦气的 Python 代码实现应该能为你提供一个可靠的蓝图，以便用其他高性能编译语言（如 C、C++、Go 或 Rust）来实现加密货币。^( ³¹ )

如果你主要对区块链和加密货币的理论感兴趣，那么只需阅读本章的“氦气配置”部分即可。



## Python 安装与虚拟环境

首要任务是创建一个用于 Helium 的虚拟环境。你需要一个大于等于 3.8 版本的 Python。本书附录 1 包含了创建此类虚拟环境的说明。

你可以在任意目录（文件夹）中创建虚拟环境，但如果你想严格遵循本书内容，那么我建议在 `/var/www/` 目录下创建虚拟环境。此外，在你的虚拟环境中安装 Python 3.8 版本。之后，你的 Helium 虚拟环境将位于以下目录（文件夹）中：

```
/var/www/helium/
```

我们将把 `/var/www/helium/` 目录称为虚拟环境的根目录，或者等同于 Helium 项目的根目录。在许多情况下，我会直接将该目录简称为 Helium 根目录。

在根目录下，创建以下 12 个子目录：`block`、`chainstate`、`config`、`crypt`、`data`、`hnetwork`、`mining`、`simulated_network`、`testnet`、`transactions`、`unit_tests` 和 `wallet`。`block` 目录将存放我们的区块链实现。`crypt` 目录将包含 Helium 所需的加密函数，而单元测试将存放在 `unit_tests` 目录中。

`data` 目录将存放 Helium 区块以及其他数据。我将在后续章节中讨论其他目录。

为了能够访问这些目录中的 Python 模块，我们必须将这些目录的路径添加到 Python 搜索路径中。请确保你已按如下方式激活了虚拟环境。从根目录执行：

```
source virtual/bin/activate
```

从根目录，进入 `virtual/lib/python3.8/site-packages/` 目录。在此目录下创建一个名为 `helium.pth` 的文件，然后将以下内容添加到该文件中：

```
/var/www/helium/block/
/var/www/helium/chainstate
/var/www/helium/config/
/var/www/helium/crypt/
/var/www/helium/data/
/var/www/helium/hnetwork/
/var/www/helium/mining/
/var/www/helium/simulated_network
/var/www/helium/testnet/
/var/www/helium/transactions/
/var/www/helium/unit_tests/
/var/www/helium/wallet
```

保存此文件，然后从根目录停用并重新激活你的环境：

```
$(virtual) deactivate
$ /bin/activate
```

你应该使用 Git 来定期将你的工作提交到仓库。进入根目录，初始化 Git 并进行首次提交：

```
$(virtual) git init
$(virtual) git add .
$(virtual) git commit -m "initial Helium commit, virtual environment created"
```

你可以使用以下命令查看提交历史：

```
$(virtual) git log
```

`git` 将使你能够在出错时回滚到之前的版本。你可能需要经常提交源代码更改。

## Helium 配置

Helium 使用几个关键的配置参数来定义这种加密货币的特性和行为。以下是这些参数的简要说明。如果你还不理解其中某些参数的含义，请不要担心。在接下来的阐述中，它们的含义和重要性会变得更加清晰。

### Helium 版本号

```
VERSION_NUMBER = 1
```

这是 Helium 应用程序的当前版本号。

### Helium 硬币的最大数量

```
MAX_HELIUM_COINS = 21_000_000
```

这是可以存在的 Helium 硬币的最大数量。我们将一个 Helium 硬币称为一个 **helium**，多个 Helium 硬币称为 **heliums**。

固定数量的 **heliums** 在创世区块中被初始创建并分配。创世区块是一个硬编码的区块，用于引导区块创建过程。在比特币中，中本聪在创世区块中创建并分配了 50 个比特币。

随后，随着新区块被添加到 Helium 区块链中，新的 **heliums** 被创建。请注意，可创建的 **heliums** 最大数量与比特币生态系统中可以产生的最大比特币数量相同。

### 最小的 Helium 货币单位

```
HELIUM_CENT = 1/100_000_000
```

`HELIUM_CENT` 是 **helium** 可能的最小细分单位。需要注意的是，一亿个 Helium 分等于一个 **helium**。这一定义与比特币中聪（satoshi）的定义相对应。

### Helium 区块大小

```
MAX_BLOCK_SIZE = 1_000_000
```

`MAX_BLOCK_SIZE` 是 Helium 区块的最大大小，以字节为单位。区块的最大大小决定了单个区块中可以包含的 Helium 交易的最大数量，*进而*也影响了区块中平均的交易数量。

### 最大交易输入数量

一笔 Helium 交易会收集来自之前交易的未花费余额，然后将这些余额转移给一个或多个收款人。`Max-Inputs` 参数限制了单笔交易中的最大输入数量。此限制有助于提高交易处理效率，并帮助防范拒绝服务攻击：

```
MAX_INPUTS = 10
```

### 最大交易输出数量

一笔 Helium 交易将价值转移给一个或多个收款人。`Max_Outputs` 参数限制了单笔交易中的最大收款人数量：

```
MAX_OUTPUTS = 10
```

### 锁定时间间隔

```
MAX_LOCKTIME = 60*1440*7
```

创建交易的实体可以指定，只有在特定时间过去之后才能处理此交易。这将阻止矿工在时间间隔过去之前将此交易包含在待挖区块中。经过的时间以秒为单位。

`MAX_LOCKTIME` 指定了从其创建时刻起，一笔交易可以被锁定的最长时间（以秒为单位）。我们不希望无限期地锁定交易，或者将交易锁定非常长的时间。这可能导致拒绝服务攻击，因为矿工维护的交易缓存会被未来的交易堵塞。

上面的配置参数将交易可以被锁定的最长时间设置为七天。

### Coinbase 间隔

```
COINBASE_INTERVAL = 100
```

这个正整数指定了在引用区块之后，必须挖出多少个新区块，然后才能花费引用区块中的 coinbase 交易。当矿工挖出一个区块时，他（她）会获得固定数量的新加密货币作为奖励。这种新货币通过区块中的 coinbase 交易授予给矿工。coinbase 间隔参数阻止矿工在区块链上添加了特定数量的新区块之前花费 coinbase 交易。

在 Helium 中，大约每十分钟挖出一个新区块，因此 Helium 的 coinbase 交易会被锁定大约 1000 分钟。

### 随机数

```
NONCE = 0
```

这是一个种子值。`NONCE` 是挖矿工作量证明计算中使用的起始值。我们稍后将讨论随机数的这种用法。

## 难度值

```
DIFFICULTY_BITS   = 20
DIFFICULTY_NUMBER =  1/ (10 ** (256 - DIFFICULTY_BITS))
```

`DIFFICULTY_BITS` 参数允许我们调整 `DIFFICULTY_NUMBER`。在 Helium 以及比特币中，挖出一个区块涉及解决一个困难的数学问题。`DIFFICULTY_NUMBER` 决定了这个问题的困难程度，进而决定了解决该问题所需的平均时间。

当这个难题被 Helium 挖矿节点解决后，就称该区块被挖出。解决该问题的节点有权将该挖出的区块附加到区块链上，并且有权获得新挖出的 **heliums** 奖励，以及该挖出区块中所有交易附带的交易费用。



#### 重定向间隔

```
RETARGET_INTERVAL = 1000
```

`Helium` 尝试每十分钟挖掘一个新的区块。当挖掘了 1000 个新区块后，`Helium` 会使用一种算法调整`DIFFICULTY_NUMBER`，以确保在平均十分钟内挖掘出一个区块。

`Bitcoin` 算法也尝试在平均十分钟内挖掘出一个新区块。与 `Bitcoin` 非常相似的 `Litecoin` 则尝试平均每两分钟挖掘一个新的区块。

### 挖矿奖励

```
MINING_REWARD = 5_000_000_000
```

`MINING_REWARD` 是以 `HELIUM_CENTS` 为单位的新增 Helium 数量，这些新增的 Helium 会被创建并奖励给成功挖掘出区块的挖矿节点。`Helium` 中的初始挖矿奖励是 50 亿 `HELIUM_CENTS`，即五枚 Helium 币（五个 helium）。这个奖励数额与 `Bitcoin` 的初始挖矿奖励相对应。

在 `Helium` 以及 `Bitcoin` 中，挖矿奖励算法会定期将挖矿奖励减半。奖励减半的发生时间取决于已经挖掘出的区块数量。由于这种定期减半机制，挖矿奖励会逐渐趋近于零。

加密货币的挖矿算法并非必须定期减半奖励。有些加密货币创建的新币数量是恒定的，或者根据某种公式变化。

`Bitcoin` 诞生于 2009 年 1 月 3 日，当时中本聪创建并挖掘了创世区块。该区块及其后挖掘的每个区块的挖矿奖励都是 50 个比特币。2012 年，挖矿奖励减半为 25 个；2016 年，奖励再次减半为 12.5 个比特币。2020 年，奖励减半至 6.25 个比特币。

由于创造新 helium 或比特币的唯一途径是挖矿过程（除了创世区块的初始分配），这两种加密货币的挖矿算法对其可被创造的总量设定了上限。因此，随着时间的推移，这些算法造成了 helium 和比特币的稀缺性，而这种稀缺性在理论上提升了这些加密货币的价值。

#### 奖励间隔

```
REWARD_INTERVAL = 210,000
```

`REWARD_INTERVAL` 决定了必须再挖掘多少个新区块才能使挖矿奖励减半。例如，在挖掘了 21 万个新区块后，挖矿奖励将会减半。这个数字与 `Bitcoin` 中使用的间隔相同。

## Helium 配置模块

我们如下为 `Helium` 创建一个配置模块。在 `config` 目录下创建一个名为 `hconfig.py` 的文件，并将以下源代码复制到该文件中：

```
"""
hconfig.py:  用于配置 Helium 的参数。
"""
conf = {
### Helium 版本号
'VERSION_NO': 1,
### 可挖掘的最大 Helium 币数量
'MAX_HELIUM_COINS': 21_000_000,
### 一个 Helium 币的最小货币单位
'HELIUM_CENT':  1/100_000_000,
### Helium 区块的最大大小（字节）
'MAX_BLOCK_SIZE': 1_000_000,
### 交易可被锁定的最大时间（秒）
'MAX_LOCKTIME': 30*1440*60,
### Helium 交易中的最大输入数量
'MAX_INPUTS': 10,
### Helium 交易中的最大输出数量
'MAX_OUTPUTS': 10,
### 从前一个参考区块算起，必须再挖掘多少个新区块后，才能使用前一个参考区块中的 coinbase 交易
'COINBASE_INTERVAL': 100,
### 挖矿工作量证明计算中的起始 nonce 值
'NONCE': 0,
#
### 挖矿工作量证明计算中使用的难度值
#
'DIFFICULTY_BITS': 20,
'DIFFICULTY_NUMBER':  1/ (10 ** (256 - 20)),
#
### 为调整 DIFFICULTY_NUMBER 而设置的区块重定向间隔
#
'RETARGET_INTERVAL': 1000,
#
### 挖矿奖励
#
'MINING_REWARD': 5_000_000_000,
#
### 挖矿奖励减半的区块间隔
#
'REWARD_INTERVAL': 210_000
}
```

## 总结

在本章中，我们通过在配置模块中指定重要的配置参数，开始了 `Helium` 加密货币的构建。这些参数影响着 `Helium` 的交易处理、挖矿和货币创造特性。当然，软件开发人员可以修改它们。我们的 `Helium` 配置是模仿 `Bitcoin` 设计的。

在下一章中，我们将开发 `Helium` 加密模块。该模块用于支持 `Helium` 执行的所有加密操作。之后，我们将开发 `Helium` 区块链。

