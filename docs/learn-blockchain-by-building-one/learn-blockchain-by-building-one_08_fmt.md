# 4. 工作量证明

本章的主要目标是清楚地解释区块是如何在区块链中被挖出的。间接地，这也解释了新货币是如何诞生的，以及组成网络的不同个体如何达成共识（就区块链的状态达成一致）。因此，在阅读完本章后，您将对实现这一目标的工作量证明（Proof of Work）协议有一个实际的了解。

我们还会看到，由于点对点网络的去中心化性质，在任何给定时间点，都不存在唯一的一个区块链。同时存在许多有效的链，但随着时间的推移，节点们会就哪条链是权威链达成共识。

在上一章中，我们实现了区块链类中的方法。我们还学习了区块如何通过哈希值“链接”在一起，并实现了创建和添加新区块的方法。扎实掌握哈希的概念至关重要，因为本章才是真正神奇之处——我们将阐明“挖矿”的概念，或者更正式地说，是创建工作量证明（proofs of work）。

## 使用 iPython 与区块链类交互

一个有助于与 Python 解释器交互的伟大工具是 `iPython`。它为您的 Python 解释器添加了制表符补全和语法高亮功能，使交互更容易查看和理解。首先启用您的虚拟环境，然后使用 `pip` 安装它：

```bash
$ pip install ipython
```

然后调用 Python 解释器：

```python
$ ipython -i funcoin/examples/chapter_3/full_blockchain.py
Python 3.8.3 (default, Oct 13 2019, 18:33:25)
Type 'copyright', 'credits', or 'license' for more information.
IPython 7.12.0 -- An enhanced Interactive Python. Type '?' for help.
In [1]:
```

尝试调用您的区块链，并对方法使用制表符补全：

```python
In [1]: bc = Blockchain()
Creating genesis block
Created block 0
In [2]: bc.h____________________
hash
chain
last_block
new_block
pending_transactions
```

展望未来，您需要主动试验您的区块链类，以获得对即将到来的概念的直观理解。

### 工作量证明简介

在创建比特币的过程中，中本聪的天才之处不在于创新，而在于融合了密码学家们为应对隐私侵蚀而先前取得的各项突破。这些突破涵盖了不同领域和主题，例如利用最初用于通过互联网共享文件的对等网络（P2P 网络）来解决中心化和信任问题；或者更具体地说，使用为打击电子邮件垃圾邮件而发明的技术来解决“铸造”货币的问题。

但比特币最大的突破在于利用工作量证明，在一个网络中对那些互不相识、更重要的是互不信任的节点之间达成共识。简而言之：在一个人人拥有平等发言权的去中心化网络中，我们需要一种就事务达成一致的方法；比如判断一笔交易是否存在欺诈，或者一条区块链是否有效。现在，我们可能有点操之过急了（我们很快会澄清大部分概念），但重要的是要退一步，纵观全局，看清谜题的各个部分是如何拼合在一起的。

工作量证明，或者更正式地说，工作量证明算法，描述了将新区块添加到区块链的方法。比特币和以太坊（目前）都使用工作量证明算法来向各自的区块链添加新区块。工作量证明算法听起来相当复杂，但实际上它们非常简单。在我们深入探讨之前，我想先抛出一个问题让你思考：*你如何向别人证明你做了工作？*

思考一两分钟。假设你在后院挖洞，发现了一块金块。这块金块能证明为了得到它付出了努力吗？我认为它可以证明*某人*做了*一些*工作。让我们换个角度——如何证明你跑了一场马拉松？你也许可以在头上绑个摄像头，一步一步地记录下整场比赛。这应该就足够了，对吧？也许吧，但录像可能被篡改过，或者可能是别人戴的摄像头。也许我们在钻牛角尖，但我想让你注意到的是，要在毫无疑义（或无需信任）的情况下*证明*工作已经完成是多么困难，尤其是当你在电脑前时，电脑可是伪造东西的能手。

用通俗的话来说，工作量证明算法就实现了这一点——它允许你证明你的电脑做了某些工作。从更技术性的角度讲，工作量证明算法是共识机制——如果你能证明工作已经完成，那么你就可以向其他人展示你的证明，而他们可以轻易地验证其真实性。这些算法有许多变体，但我们将专注于一个简单的例子来帮助你理解。

我们已经解释过，POW 是用于挖掘新区块的算法。工作量证明算法的实际方法很简单——找到一个满足某个小型数学问题的数字：从计算的角度来看，这个数字必须难以找到，但网络上的任何人都可以轻松验证。这就是工作量证明背后的核心思想：*难以完成，但易于验证*。我们将看一个非常简单的例子来帮助你加深理解。

## 工作量证明的一个简单示例

我们规定，某个整数 `x` 乘以另一个整数 `y` 的 *哈希值* 必须以 `0` 结尾。所以，`hash(x * y) = dedb2ac23dc...0`。在这个简化示例中，我们设定 `x = 5`。用 Python 实现如下：

```
1  from hashlib import sha256

4  x = 5
5  y = 0  # 我们还不知道 y 应该是多少...

7  while  sha256(f'{x*y}'.encode()).hexdigest()[-1] != "0":
8      y += 1

10  print(f'解为 y = {y}')
```

这里的解是 `y = 21`。因为生成的哈希值以 `0` 结尾：

```
>>> sha256(f"{5 * 21}".encode()).hexdigest()
'1253e9373e781b7500266caa55150e08e210bc8cd8cc70d89985e3600155e860'
```

在比特币中，工作量证明算法有一个名称，叫做 *Hashcash*。它与前面那个基础示例差别不大。矿工们正是竞相求解这个算法来创建新区块。通常，难度由字符串开头的零的个数决定；矿工会因其解而获得奖励——通过一笔交易接收一定数量的比特币；而网络则能够像我们之前做的那样，轻松地*验证*他们的解答。

## 类比：拼图游戏

拼图游戏是工作量证明算法的一个很好的类比，因为工作量体现在人类对一张图像的一致识别上。如果我们让电脑来解开一幅拼图：

![../images/475256_1_En_4_Chapter/475256_1_En_4_Figa_HTML.jpg](img/475256_1_En_4_Figa_HTML.jpg)

它就必须穷举每一种组合：

![../images/475256_1_En_4_Chapter/475256_1_En_4_Figb_HTML.jpg](img/475256_1_En_4_Figb_HTML.jpg)

并且它会一直这样循环下去，直到我们看到图像出现时按下“停止”按钮：

![../images/475256_1_En_4_Chapter/475256_1_En_4_Figc_HTML.jpg](img/475256_1_En_4_Figc_HTML.jpg)

现在你可以把这幅图像展示给别人，他们就能毫无疑问地知道，工作已经完成了。

## 实现工作量证明

现在，我们来为区块链实现一个类似的算法。规则与上述例子相似：

找到一个数字 `p`，当它与前一个区块的解一起进行哈希运算时，会生成一个以四个 0 开头的哈希值。

继续沿用上一章的示例，我们为 `Blockchain` 类添加两个新方法：`proof_of_work` 和 `valid_hash`，分别用于挖矿和验证哈希值：

```python
1  import json

3  from datetime import datetime
4  from hashlib import sha256

7  class Blockchain(object):
8      def __init__ (self):
9          self.chain = []
10          self.pending_transactions = []

12          # 创建创世区块
13          print("Creating genesis block")
14          self.new_block()

16      def new_block(self, previous_hash=None):
17          block = {
18              'index': len(self.chain),
19              'timestamp': datetime.utcnow().isoformat(),
20              'transactions': self.pending_transactions,
21              'previous_hash':  previous_hash,
22              'nonce': None,
23          }

25          # 获取新区块的哈希值，并将其添加到区块中
26          block_hash = self.hash(block)
27          block["hash"] = block_hash

29          # 重置待处理交易列表
30          self.pending_transactions = []
31          # 将区块添加到链中
32          self.chain.append(block)

34          print(f"Created block {block['index']}")
35          return block

37      @staticmethod
38      def hash(block):
39          # 确保字典有序，否则哈希值会不一致
40          block_string = json.dumps(block, sort_keys=True).encode()
41          return sha256(block_string).hexdigest()

43      def last_block(self):
44          # 返回链中的最后一个区块（如果存在）
45          return self.chain[-1] if self.chain else None

47      def proof_of_work(self):
48          pass

50      def valid_hash(self):
51          pass
```

我们还在区块结构中添加了一个名为 `nonce` 的新字段，它代表一个无意义的字符串。可以将 `nonce` 视为一次性随机数，它将作为我们区块的重要随机性来源。对于这些 `nonce`，我们将使用 Python 的 `random` 模块生成一个随机的 64 位十六进制数：

```python
import random
>>> format(random.getrandbits(64), "x")
'828ad30173db207b'
```

现在来充实我们的两个方法：`proof_of_work` 和 `valid_hash`，先从后者开始。`valid_hash` 很简单；它需要检查一个区块的哈希值是否以特定数量的零开头，比如 4 个零：

```python
@staticmethod
def valid_block(block):
    return block["hash"].startswith("0000")
```

现在，让我们实现一个简单的工作量证明算法，该算法会创建一个新区块并检查其是否具有有效的哈希值：

```python
1  def proof_of_work(self):
2      while True:
3          new_block = self.new_block()
4          if self.valid_block(new_block):
5              break
6
7      self.chain.append(new_block)
8      print("Found a new block: ", new_block)
```

代码不言自明：

1. 我们创建一个新区块（其中包含一个随机的 `nonce`）。
2. 然后对区块进行哈希运算，检查其是否有效。
3. 如果有效，则返回该区块；否则，从步骤 1 开始无限重复。

让我们将这些方法添加到我们的 `Blockchain` 类中：

```python
1  import json
2  import random
3
4  from datetime import datetime
5  from hashlib import sha256
6
8  class Blockchain(object):
9      def __init__ (self):
10          self.chain = []
11          self.pending_transactions = []
12
13          # 创建创世区块
14          print("Creating genesis block")
15          self.chain.append(self.new_block())
16
17      def new_block(self):
18          block = {
19              'index': len(self.chain),
20              'timestamp': datetime.utcnow().isoformat(),
21              'transactions': self.pending_transactions,
22              'previous_hash': self.last_block["hash"] if self.last_block else None,
23              'nonce': format(random.getrandbits(64), "x"),
24          }
25
26          # 获取新区块的哈希值，并将其添加到区块中
27          block_hash = self.hash(block)
28          block["hash"] = block_hash
29
30          # 重置待处理交易列表
31          self.pending_transactions = []
32
33          return block
34
35      @staticmethod
36      def hash(block):
37          # 确保字典有序，否则哈希值会不一致
38          block_string = json.dumps(block, sort_keys=True).encode()
39          return sha256(block_string).hexdigest()
40
41      @property
42      def last_block(self):
43          # 返回链中的最后一个区块（如果存在）
44          return self.chain[-1] if self.chain else None
45
46      @staticmethod
47      def valid_block(block):
48          # 检查区块的哈希值是否以 0000 开头
49          return block["hash"].startswith("0000")
50
51      def proof_of_work(self):
52          while True:
53              new_block  =  self.new_block()
54              if self.valid_block(new_block):
55                  break
56
57          self.chain.append(new_block)
58          print("Found a new block: ", new_block)
```

我们需要进行两个重要的删除操作：

- 删除 `new_block()` 方法中的 `print` 语句，否则在挖矿时，控制台会出现大量不需要的输出。
- 在第 33 行，`new_block()` 方法内部，删除 `self.chain.append(block)` 这一行（我们不希望未经验证的区块）被添加到链中。

现在是时候启动 `ipython`，实例化 `Blockchain` 类，然后开始挖一些区块了：

```python
In [1]: from blockchain import Blockchain
In [2]: bc = Blockchain()
Creating genesis block
In [3]: bc.proof_of_work()
Found a new block:
{
"index": 1,
"timestamp": "2020-02-07T04:39:03.920459",
"transactions": [],
"previous_hash": "1a93bd623f3e3aba2c9d4ee9193d36904382e7416b2fb854dbf3fc6a13cab702",
"nonce":  "8b9991ef3ea9eb19",
"hash": "0000075c4a6d3ae6ab06677887618cf41255d7a1ba10220bb3e9bc8c8ecec80e"
}
In [4]: bc.proof_of_work()
Found a new block:
{
"index": 2,
"timestamp": "2020-02-07T04:39:14.440383",
"transactions": [],
"previous_hash": "0000075c4a6d3ae6ab06677887618cf41255d7a1ba10220bb3e9bc8c8ecec80e",
"nonce": "b9a579d2cfa587b0",
"hash": "00009d974a58fb689ad280c121897eb2f6da699db2c4bd2a3312dc41d478c182"
}
In [5]: bc.proof_of_work()
Found a new block:
{
"index": 3,
"timestamp": "2020-02-07T04:39:20.744179",
"transactions": [],
"previous_hash": "00009d974a58fb689ad280c121897eb2f6da699db2c4bd2a3312dc41d478c182",
"nonce":  "f4de39bb07bce043",
"hash": "000003910a7ad733f89a9ff13ffea5b5e08fe69d581a4ecd30f237f95800221e"
}
```

为了调整挖矿的难度，我们可以修改前导零的数量。但四个零已经足够了。你会发现，每增加一个前导零，找到解所需的时间就会呈指数级增长。在比特币中，难度会通过网络的共识每 2016 个区块调整一次。提高难度是为了应对硬件不可避免的加速发展。

## 货币供应

现在你已经拥有一个完全可运行的区块链类，它通过实现挖矿来创建新区块。这非常接近比特币等生产级区块链中挖矿的实际方式。正是这个挖矿算法生成了新的比特币：当矿工完成一个区块的挖矿时，会插入一笔来自无主的、向自己的交易，交易的比特币数量每 210,000 个区块减半。这就是新比特币的诞生方式。