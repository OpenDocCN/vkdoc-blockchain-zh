# 6. 索引与存储

在过去的两个章节中，我们学习了如何从以太坊网络读取数据和向以太坊网络写入数据。读取可以通过对合约进行常规调用或查询已记录的事件来完成，而写入则总是在交易的上下文中执行。然而，这些操作仅适用于基本用例。如果我们想要展示来自区块链的聚合数据，客户端查询事件很快便会力不从心。同样，如果我们想在合约中存储大量信息，Gas 成本使其在经济上不可行。在本章中，我们将使用链下组件来解决这两个问题。我们首先将介绍在服务器中对区块链数据进行索引的过程，以便从客户端应用程序查询数据，然后介绍链下存储解决方案。在此过程中，我们还将回顾处理重组和测试的技术，并讨论中心化与去中心化解决方案的价值。

## 索引区块链数据

我们将从索引区块链数据的问题入手。在此语境下，*索引*一词指的是从网络中收集特定信息（如代币余额、已发送的交易或已创建的合约），并将其存储在可查询的数据存储中（例如关系型数据库或 ElasticSearch 等分析引擎），以便执行复杂查询的过程。在本章中，我们不会尝试索引整个链，而是会选择特定的数据集，并为其构建定制的解决方案。

当你需要对记录数据进行任何类型的*聚合*（例如求和或求平均值）时，索引就是必要的，因为以太坊事件 API 并不适合执行此类操作。例如，无法轻易获得持有超过某个 ERC20 资产余额的唯一地址数量——而这种查询在关系型数据库中执行起来非常简单。

索引还可以通过充当查询缓存来提高查询大量事件时的性能。专用数据库回答事件查询的速度比普通以太坊节点快得多。

### 说明

某些公共节点提供商（如 Infura）实际上在其节点之外，还包含了一个事件查询层（⁸¹），这极大地减少了它们为提供日志查询所耗费的基础设施资源。

现在，我们将专注于一个具体的索引用例，并设计一个用于收集信息并建立索引的解决方案。

### 追踪 ERC20 代币持有者

我们将追踪特定 ERC20 代币的持有者。请记住，ERC20 非同质化代币可以充当一种代币，每个地址都有一定的余额。然而，合约并未提供任何方法来实际列出它们。即使提供了，某些代币的用户基数也可能远超仅查询以太坊节点的客户端应用的处理能力。例如，在撰写本文时，OmiseGO (OMG) 代币拥有超过 65 万个唯一持有者（⁸²）。

#### 重温 ERC20 接口

我们将回顾 `ERC20` 合约接口，以确定我们将要使用的构建模块。抛开与授权额度相关的功能不谈，`ERC20` 标准包含以下方法和事件。

```
function transfer(address to, uint256 value) returns (bool);
function totalSupply() view returns (uint256);
function balanceOf(address who) view returns (uint256);
event Transfer(
address indexed from, address indexed to, uint256 value
);
```

如前所述，与扩展的 `ERC721` 标准不同，`ERC20` 并未提供列出所有代币持有者的方法。构建这样一个列表的唯一方法是遍历每一个 `Transfer` 事件，并收集所有接收者地址。

### 注意

某些 `ERC20` 合约在铸造新代币时不会发出 `Transfer` 事件，而是会发出一个非标准的 `Mint` 事件。在这种情况下，我们需要追踪这两个事件，才能构建完整的持有者集合。

### 查询转账事件

我们将从查询给定合约的所有转账事件开始。我们将构建一个 `ERC20Indexer` 类，它依赖于一个 web3 连接提供者、一个 `ERC20` 代币地址以及一个开始查询的区块号（清单 6-1）。添加最后一个参数仅出于性能考虑：查询代币合约部署之前的任何区块是没有意义的。

```
// 01-indexing/simple-indexer.js
const ERC20 = require('openzeppelin-solidity/build/contracts/ERC20.json');
const BigNumber = require('bignumber.js');
const Web3 = require('web3');
class ERC20Indexer {
constructor({ address, startBlock, provider } = {}) {
this.holders = new Set();
this.startBlock = startBlock;
this.lastBlock = this.startBlock;
this.web3 = new Web3(provider);
this.contract = new this.web3.eth.Contract(
ERC20.abi, address
);
}
}
```

**清单 6-1**
我们将要处理的 `ERC20Indexer` 类的构造函数。我们再次依赖 `openzeppelin-solidity@2.2.0` 来获取 `ERC20` 合约的 ABI。我们将把所有代币持有者地址的列表存储在 `holders` 集合中，`lastBlock` 字段将记录我们已处理的最后一个区块。

现在我们可以为此类添加第一个方法，用于获取给定合约的转账事件列表（清单 6-2）。由于这个列表可能超过单次请求所能容纳的大小（OMG 到目前为止已有超过 200 万笔转账），我们需要将其拆分为多个请求，因此我们将从一个处理区块范围内所有事件的方法开始。

```
async processBlocks(startBlock, endBlock) {
for (let fromBlock = startBlock;
fromBlock  this.reduceEvent(e));
}
}
```

**清单 6-2**
批量查询某个区块范围内的所有转账事件。这里的 `BATCH_SIZE` 应取决于合约每个区块的交易量。

此方法将从合约获取某个区块范围内的所有转账事件，并将其发送给一个 `reduceEvent` 函数（清单 6-3）。这个归约函数应接收一个事件并使用它来更新当前的代币持有者列表。

```
const ZERO_ADDRESS = '0x0000000000000000000000000000000000000000';
async reduceEvent(event) {
const { to } = event.returnValues;
if (to !== ZERO_ADDRESS) {
this.holders.add(to);
}
}
```

**清单 6-3**
检索代币转账的接收者并将其添加到代币持有者列表中。转账到零地址通常代表代币被销毁，因此我们会将其排除在列表之外。

### 注意事项

在生产级实现中，你不应将持有者列表存储在内存中的 JavaScript 数据结构里，因为进程停止时该数据会被清空。更建议使用数据库来存储所有数据并响应客户端的查询请求。数据库引擎的选择不在本书讨论范围内，且在很大程度上取决于你的具体用例。

不过这种方法可能会产生一些误报。如果一个地址曾持有余额但后续全部转出，它会被添加到持有者列表中却永远不会被移除。这意味着我们需要验证地址余额非零才能将其列入列表。在此基础上，我们将更进一步，追踪每个持有者的当前余额。有两种实现方案，各有优劣：

- 我们可以依赖 `ERC20` 的 `balanceOf` 方法检查每个待添加地址的余额。一旦发现新地址即可立即查询，并准备好带有余额的持有者信息。但这意味着需要为每个代币持有者发起额外请求，并且每当发现包含新交易的新区块时都需要重新执行此查询。

- 我们可以利用 `ERC20` 合约中任何地址的余额仅通过查看转账事件即可确定的特性。由于我们已经在查询这些事件，可以追踪每个地址的转入转出动向并实时更新余额。这需要在代码端增加更多逻辑，但无需向以太坊网络发送额外请求。其缺点是在完成直到链上最新区块的扫描之前，我们无法依赖任何地址的余额。

为了优先减少请求次数，我们将采用第二种策略（清单 6-4）。这意味着我们的事件规约函数还需要处理每笔转账的*数值*。

```javascript
async reduceEvent(event) {
  const { from, to, value } = event.returnValues;
  if (from !== ZERO_ADDRESS) {
    this.balances[from] = this.balances[from]
      ? this.balances[from].minus(value)
      : BigNumber(value).negated();
  }
  if (to !== ZERO_ADDRESS) {
    this.balances[to] = this.balances[to]
      ? this.balances[to].plus(value)
      : BigNumber(value);
  }
}
```

**清单 6-4**
通过规约事件更新每个持有者的余额。这里的 `balances` 是一个替代了原有持有者集合的对象。请注意我们排除了零地址作为发送方和接收方的情况，因为来自零地址的转账代表铸币事件，而发往零地址的转账则代表销毁事件。

有了能够生成指定区块范围内持有者及其余额列表的类之后，我们需要在新区块被挖出时保持此列表的更新（清单 6-5）。我们将采用轮询方式获取新区块，不过订阅模式也是可行的替代方案。

```javascript
const CONFIRMATIONS = 12;
const INTERVAL = 1000;

async processNewBlocks() {
  const lastBlock = this.lastBlock;
  const currentBlock = await this.web3.eth.getBlockNumber();
  const confirmedBlock = currentBlock - CONFIRMATIONS;
  if (!lastBlock || confirmedBlock >= lastBlock) {
    await this.processBlocks(lastBlock, confirmedBlock);
    this.lastBlock = confirmedBlock + 1;
  }
}

start() {
  this.timeout = setTimeout(async () => {
    await this.processNewBlocks()
    this.start();
  }, INTERVAL);
}
```

**清单 6-5**
轮询新区块并获取所有新增的转账事件。`processNewBlocks` 函数会查询最新区块，并调用 `processBlocks` 处理新区块范围，而 `start` 函数则启动一个无限循环，持续轮询后休眠 1 秒。

实现中的一个重要细节是：我们不会处理到最新区块，而只处理到一定数量的区块之前。这能确保我们处理的所有转账事件都已得到确认，不会因链重组而被回滚。后续我们将探讨如何查询最新区块以及检测到重组时的处理策略。

### 共享数据

最后一步是实际提供已收集数据的访问接口（清单 6-6）。我们将搭建一个简单的 Express 服务器，通过 HTTP GET 请求以 JSON 格式公开余额数据。

```javascript
const express = require('express')
const mapValues = require('lodash.mapvalues');

// 测试主网代币的示例值
const API_TOKEN = 'YOUR_INFURA_API_TOKEN';
const PROVIDER = 'https://mainnet.infura.io/v3/' + API_TOKEN;
const ADDRESS = '0x00fdae9174357424a78afaad98da36fd66dd9e03';
const START_BLOCK = 6563800;

// 初始化 express 应用和索引器
const app = express()
const indexer = new Indexer({
  address: ADDRESS,
  startBlock: START_BLOCK,
  provider: PROVIDER
});

// 注册查询余额的路由
app.get('/balances.json', (_req, res) => {
  res.send(
    mapValues(indexer.balances, b => b.toString(10))
  );
});

// 启动！
app.listen(3000);
indexer.start();
```

**清单 6-6**
通过 HTTP 端点暴露索引器余额的简单 Express 服务器。使用 lodash 的 `mapValues` 工具函数在序列化为 JSON 前格式化 `BigNumber` 值。请确保获取 Infura 令牌以设置 `API_TOKEN` 变量。

我们可以通过 `node` 运行此脚本，在另一个控制台使用 `curl` 查询余额来测试（清单 6-7）。请等待几秒让脚本处理一些区块以收集转账数据。

```bash
$ curl -s "localhost:3000/balances.json" | jq .
{
  "0xB048...": "26000000000000000000000",
  "0x58b2...": "77997707535390983471493397",
  "0x352B...": "4666667000000000000000000",
  "0xBC96...": "3000000000000000000000000",
  ...
}
```

**清单 6-7**
查询 Express 端点获取代币余额。我们使用 `jq` 工具美化 JSON 输出。

这个基础实现利用了所有索引数据都存储在索引器实例中的特性。请记住，在实际部署中，你需要将所有数据存储在独立的数据库（如关系型数据库）中。这样既能直接对数据库执行查询，又能解耦 Web 服务器和索引器进程使它们独立运行，还能根据客户端需要添加聚合、过滤、排序和分页功能：在单个请求中返回包含五十万个代币持有者的列表在某些场景下可能表现不佳。

### 处理链重组

到目前为止，我们通过仅处理可以视为最终确认的转账（即发生在足够多区块之前的转账，这些区块被从链中移除的概率可以忽略不计）来规避链重组问题。现在我们将移除这一限制，探讨如何通过正确响应重组来安全处理最新事件。

#### 使用订阅

检测并响应重组的最简单方法是通过订阅。对某个事件（例如 ERC20 的 `Transfer`）进行订阅，不仅会将新事件实时推送到我们的进程，还会通知因重组而*移除*的任何事件。这意味着我们可以编写 `reduceEvent` 函数的对应函数来撤销事件，并在以太坊节点推送事件移除时运行它（代码清单 6-8）。

```
// 01-indexing/subs-indexer.js
undoTransfer(event) {
  const { from, to, value } = event.returnValues;
  if (from !== ZERO_ADDRESS) {
    this.balances[from] = this.balances[from].plus(value);
  }
  if (to !== ZERO_ADDRESS) {
    this.balances[to] = this.balances[to].minus(value);
  }
}
```
*代码清单 6-8 在我们的余额列表中撤销一个转账事件。此处的逻辑与 `reduceEvent` 方法中的逻辑相反。*

这样，我们就不必依赖 `getPastEvents` 方法来轮询新区块，而是可以简单地打开一个订阅并监听事件的添加和移除（代码清单 6-9）。

```
async function start() {
  // Process all past blocks
  const currentBlock = await this.web3.eth.getBlockNumber();
  await this.processBlocks(this.startBlock, currentBlock);
  // Subscribe to new ones
  this.subscription = this.contract.events
    .Transfer({ fromBlock: currentBlock + 1 })
    .on('data', e => this.reduceEvent(e))
    .on('changed', e => this.undoTransfer(e.returnValues))
    .on('error', err => console.error("Error", err));
}
```
*代码清单 6-9 使用订阅来监控新事件以及因重组而移除的事件。当有新事件时触发 `data` 处理器，当事件因重组从链上移除时触发 `changed` 处理器。*

然而，这种方法有一个主要缺点。如果在发生重组时与节点的 websocket 连接断开，我们的脚本将永远不会收到移除事件的通知。这意味着我们无法回滚已被撤销的转账，最终导致状态无效。另一个问题是订阅只返回新事件，因此如果在 `processBlocks` 调用与订阅建立之间有任何区块被挖出，我们可能会错过一些事件。接下来让我们尝试一种不同的、更简单的方法。我们首先需要手动检测重组的发生。

#### 检测重组

当一条链分叉累积了比当前主链更多的总算力时，就会发生重组，并且该分叉成为正式链。如果不同的矿工集合在不同分叉上工作，就可能发生这种情况。

这意味着在重组中，一个或多个区块（从当前主链开始向前追溯）将被其他区块替换。这些新区块可能包含也可能不包含与之前区块相同的交易，并且它们的顺序也可能不同^((84))，从而产生不同的结果。

我们可以通过检查区块标识符来检测重组。回顾第 3 章，每个区块都由其哈希值标识。这个哈希值是根据区块的数据和前一个区块的哈希值计算得出的。这就是区块链的本质：每个区块都与前一个区块相连。这意味着，如果不强制更改所有后续区块的标识符，就无法更改旧区块。

这正是我们能轻松检测重组的原因。当给定高度的区块的哈希值发生变化时，意味着该区块以及它之前的其他区块也可能发生了变化。然后，我们只需监视我们处理过的最后一个区块，如果其哈希值在任何时候发生变化，我们就向后扫描其他已变化的哈希值，直到检测到一个共同的祖先（代码清单 6-10）。

让我们修改脚本，使用此策略添加重组检查，这将要求我们跟踪已处理区块的哈希值。

```
// 01-indexing/reorgs-indexer.js
async processNewBlocks() {
  // Track current block number and its hash
  const currentBlockNumber =
    await this.web3.eth.getBlockNumber() - CONFIRMATIONS;
  const currentBlockHash =
    await this.getBlockHash(currentBlockNumber);
  // Check for possible reorgs
  if (this.lastBlockNumber && this.lastBlockHash) {
    const newLastBlockHash
      = await this.getBlockHash(this.lastBlockNumber);
    if (this.lastBlockHash !== newLastBlockHash) {
      // There was a reorg! Undo all blocks affected,
      // and reprocess blocks starting by the most recent
      // one that was not removed from the main chain
      const lastBlock = await this.undoBlocks();
      this.lastBlockHash = lastBlock.hash;
      this.lastBlockNumber = lastBlock.number;
    }
  }
  // Process blocks from lastBlockNumber until currentBlock
  // Update this.lastBlockNumber and this.lastBlockHash
  // ...
}
async getBlockHash(number) {
  const { hash } = await this.web3.eth.getBlock(number);
  return hash;
}
```
*代码清单 6-10 更新后的 `processNewBlocks` 函数，在每次迭代时检查重组。`undoBlocks` 函数（代码清单 6-12）应撤销所有与已移除区块相关的转账，并返回未受重组影响的最新区块。*

请注意，我们现在不仅跟踪最新的区块号，还跟踪其哈希值。每当我们开始新的迭代时，我们都会检查同一高度区块的哈希值是否发生变化。如果发生了变化，则说明我们遇到了重组，必须撤销所有来自已移除区块的更改。

#### 撤销更改

当检测到重组时，我们需要撤销从被移除区块中已处理的所有转账。为此，我们首先需要追踪在每个区块上处理了哪些转账（代码清单 6-11）。

我们将向 `Indexer` 类添加一个新字段：一个栈结构，其中包含处理转账事件时遇到的每个区块对应的一个条目。每个条目将包含区块编号、哈希值以及该区块包含的转账列表。每当处理一个新事件时，我们都会向其中添加新条目。

```
const last = require('lodash.last');
async saveEvent(event) {
// 如果此事件发生在新区块上，添加新区块
if (!last(this.eventsBlocks)
|| last(this.eventsBlocks).hash !== event.blockHash) {
this.eventsBlocks.push({
number: event.blockNumber,
hash: event.blockHash,
transfers: []
});
}
// 将转账事件包含在最新区块中
last(this.eventsBlocks)
.transfers.push(event.returnValues);
}
```

*代码清单 6-11 — 追踪每个区块的转账事件。此函数由 `reduceEvent` 调用。请注意，当处理新事件时，必须按顺序调用此函数，以确保区块列表保持有序，且最新区块位于列表末尾。*

现在我们有了已处理区块的列表，可以从末尾开始遍历，并撤销我们在余额列表中汇总的所有转账事件（代码清单 6-12）。

```
async undoBlocks() {
while (this.eventsBlocks.length > 0) {
// 检查最后一个区块的哈希值是否发生变化
const lastBlock = last(this.eventsBlocks);
const hash = await this.getBlockHash(lastBlock.number);
// 如果未变化，则表明之前的所有区块也未变化
if (lastBlock.hash === hash)
return lastBlock;
// 如果已变化，则撤销该区块的所有转账，并继续迭代
this.eventsBlocks.pop();
lastBlock.transfers.forEach((t) =>
this.undoTransfer(t)
);
}
// 如果没有更多区块，则返回空区块
return { hash: null, number: null };
}
```

*代码清单 6-12 — 反向遍历已处理事件列表并撤销所有转账。此函数必须返回重组中未被移除的最新区块，以便脚本能从此处重新处理链。函数 `undoTransfer` 类似于本章前面订阅小节中介绍的函数。*

总结一下，为了使脚本能够抵御重组，我们实施的更改如下：

1. 在处理转账事件时，将其区块编号和哈希值保存到列表中，并在末尾追加最新区块的信息。
2. 在检查新区块时，验证我们已处理的最后一个区块的哈希值是否已更改。
3. 如果哈希值已更改，则从最近更改的区块开始，撤销每个已更改区块上发生的所有转账事件。当遇到未更改的区块时，停止操作。
4. 将最新区块重置为未更改的区块，并从中断处恢复处理。

虽然这些更改给我们的解决方案带来了许多复杂性，但它们确保了系统状态不会因重组而失去同步。如何处理它们取决于你的具体用例：可以忽略最近区块直到它们被确认，可以使用订阅让节点在假设连接稳定的情况下追踪移除的事件，或者实现类似于本方案中基于客户端的设计。

### 单元测试

到目前为止，我们忽略了软件开发的一个关键方面：测试。虽然在以太坊中进行测试与其他应用程序没有本质区别，但我们将使用索引器示例来介绍一些特定于区块链测试的有用技术。

#### 选择节点

第一个决定在于为测试选择哪个节点。回想一下前面的章节，我们可以使用 `ganache`（一个区块链模拟器），或者在实际节点（如以开发模式运行的 `Geth` 或 `Parity`）上工作。前者更轻量，并提供了额外的操作方法，可用于操作区块链状态，这在单元测试中非常有用。另一方面，使用实际节点更能代表应用程序的实际生产环境。

一个好的折衷方案是在单元测试中使用带有即时密封功能的 `ganache`，而对于端到端集成测试，则可以使用以固定区块时间运行的 `Geth` 或 `Parity` 开发节点。这允许在单元测试中进行更细粒度的控制，并在集成测试中提供更具代表性的环境。

### 注意

单元测试是否应该允许调用外部服务通常是软件开发中一个颇具争议的问题。在传统应用中，一些开发者倾向于设置测试数据库来支持他们的单元测试，而另一些开发者则模拟所有对其的调用，以便隔离测试代码，他们认为单元测试必须只执行单段代码。在这里，我们将站在前者一边，并在单元测试中连接到`ganache`实例。无论如何，这只是我们如何理解单元测试的语义问题。

#### 测试我们的索引器

现在，我们将为索引器编写一些单元测试。我们将使用`ganache`作为后端，`mocha`作为测试运行器，`chai`配合`bignumber`插件来编写断言。

第一步是部署一个`ERC20`代币供我们的索引器监控（清单 6-13）。我们将基于OpenZeppelin的默认`ERC20`实现进行扩展，增加一个公共铸造方法，以便轻松为想要测试的任何地址铸造代币。

```
// 01-indexing/contracts/MockERC20.sol
pragma solidity ⁰.5.0;
import "openzeppelin-solidity/contracts/token/ERC20/ERC20.sol";
contract MockERC20 is ERC20 {
constructor () public { }
function mint(address account, uint256 amount) public {
_mint(account, amount);
}
}
```
清单 6-13
具有公共铸造方法的`ERC20`代币合约，允许任何人创建新代币。请勿在生产环境使用！

现在，我们来为测试文件创建模板代码（清单 6-14）。我们需要导入所有相关组件，创建一个新的`web3`实例来与我们的`ganache`实例交互，并部署新合约。

```
// 01-indexing/test/indexer.test.js
const expect =
require('chai').use(require('chai-bignumber')()).expect;
const ERC20Artifact =
require('../artifacts/MockERC20.json').compilerOutput;
const Web3 = require('web3');
const web3 = new Web3('http://localhost:9545');
const ERC20 = new web3.eth.Contract(
ERC20Artifact.abi, null,
{ data: ERC20Artifact.evm.bytecode.object }
);
describe('indexer', function () {
before('setup', async function () {
this.accounts = await web3.eth.getAccounts();
this.erc20 = await ERC20.deploy().send({
from: this.accounts[0], gas: 1e6
});
});
});
```
清单 6-14
索引器测试套件的模板代码。它初始化了一个新的`web3`实例，并部署了一个`ERC20`代币合约实例。注意，为简洁起见，省略了一些`require`语句

利用这个模板，我们现在可以在`describe`块中编写第一个测试，以检查合约中铸造的任何余额是否被索引器正确捕获（清单 6-15）。

```
it('record balances from minting', async function () {
const indexer = new Indexer({
address: this.erc20.options.address
provider: 'http://localhost:9545'
});
const [from, holder] = this.accounts;
await this.erc20.methods.mint(holder, 1000).send({ from });
await indexer.processNewBlocks();
expect(indexer.getBalances()[holder])
.to.be.bignumber.eq("1000");
});
```
清单 6-15
检查通过铸造产生的转账是否被正确处理的测试。我们为刚部署的代币合约初始化一个新的`Indexer`实例，为一个地址铸造一些代币，然后运行索引器来检查结果

我们可以用`mocha`运行这个测试。请确保在另一个终端中运行了一个`ganache`实例，并监听9545端口（清单 6-16）。

```
$ ganache-cli -p 9545
$ npx mocha
```
清单 6-16
分别启动`ganache`实例和运行测试套件。每个命令应在不同的终端中运行

然而，如果我们运行测试，它会失败——索引器不会为持有者地址捕获任何余额。这是因为我们构建的索引器会忽略最新区块中的任何信息，只考虑经过一定数量确认后的转账。

我们将使用`ganache`的`mine`方法来解决这个问题（清单 6-17）。这个方法在Geth或Parity中不可用，它会指示`ganache`模拟挖出一个新区块。它像其他消息一样，通过底层JSON-RPC接口发送。

```
function rpcSend(web3, method, ... params) {
return require('util')
.promisify(web3.currentProvider.send)
.call(web3.currentProvider, {
jsonrpc: "2.0", method, params
});
}
function mineBlocks(web3, number) {
return Promise.all(
Array.from({ length: number }, () => (
rpcSend(web3, "evm_mine")
))
);
}
```
清单 6-17
辅助方法，用于指示`ganache`挖出指定数量的区块。`mineBlocks`中的代码会并行发送指定数量的请求，并在所有请求成功后返回

现在，我们可以在测试中，在指示索引器处理新区块之前立即添加对`mineBlocks`的调用，然后重新运行测试，它会通过。

#### 使用快照

现在，我们可以编写更多测试来覆盖索引器的其他场景。然而，在任何良好的测试套件中，所有测试都应相互独立，但这里并非如此，因为我们只部署了一个`ERC20`合约实例。这意味着某个测试中发生的任何铸造或转账操作，都会延续到后续测试中。

虽然明显的解决方案是将`before`步骤替换为`beforeEach`步骤，以便为每个测试部署一个新的`ERC20`合约，但我们将借此机会引入`ganache`特有的一个新概念：快照（清单 6-18）。快照允许我们保存模拟区块链的当前状态，运行一系列操作，然后回滚到保存的状态。

```
function takeSnapshot(web3) {
return rpcSend(web3, "evm_snapshot").then(r => r.result);
}
function revertToSnapshot(web3, id) {
return rpcSend(web3, "evm_revert", id);
}
```
清单 6-18
用于创建新快照（返回快照ID）和根据指定ID回滚到特定快照的辅助函数

快照的一个良好用例是通过消除为每个测试重新创建新状态的需求来节省时间（清单 6-19）。我们不必在每个测试中部署新的`ERC20`合约，只需部署一次，保存快照，执行一个测试，然后恢复快照以清除所有更改，再进行下一个测试。虽然这不会给我们的测试带来性能提升，但对于需要创建多个合约的更复杂设置套件来说，这将大有裨益。

```
beforeEach('take snapshot', async function () {
this.snapshotId = await takeSnapshot(web3);
});
afterEach('revert to snapshot', async function () {
await revertToSnapshot(web3, this.snapshotId);
});
```
清单 6-19
在每个测试之前创建新快照，并在测试结束后回滚到该快照。注意，同一个快照不能回滚多次，因此需要为每个测试创建一个新快照

快照的另一个有趣用例是测试*重组*。在常规的Geth或Parity节点上触发重组很复杂，因为它需要搭建自己的私有节点网络，并通过断开和重新连接节点来模拟链分叉。另一方面，在`ganache`上测试重组要简单得多：我们可以创建一个快照，挖出几个区块，然后回滚并挖出另一组具有不同交易集的区块。

### 注意

这不等同于实际的链重组，因为订阅不会报告任何*已移除*的事件。不过，由于我们的索引器依赖简单的轮询来检测任何变化，在这种情况下使用快照是可行的。

让我们编写一个测试，通过撤销已移除区块中的所有余额变动并处理新区块，来检验索引器能否正确处理链重组。

```
it('handles reorganizations', async function () {
  // 设置一个新的索引器
  const indexer = new Indexer({
    address: this.erc20.options.address,
    provider: 'http://localhost:9545'
  });
  // 为发送者账户铸造余额并拍摄快照
  const [from, sender, r1, r2] = this.accounts;
  await this.erc20.methods.mint(sender, 1000).send({ from });
  const snapshotId = await takeSnapshot(web3);
  // 向 r1 和 r2 转账 200 个代币，并挖掘确认区块
  const transfer = this.erc20.methods.transfer;
  await transfer(r1, 200).send({ from: sender });
  await transfer(r2, 200).send({ from: sender });
  await mineBlocks(web3, 12);
  // 运行索引器并断言这些转账已被捕获
  await this.indexer.processNewBlocks();
  const balances = this.indexer.getBalances();
  expect(balances[sender]).to.be.bignumber.eq("600");
  expect(balances[r1]).to.be.bignumber.eq("200");
  expect(balances[r2]).to.be.bignumber.eq("200");
  // 回滚以模拟重组并发送新的转账
  await revertToSnapshot(web3, snapshotId);
  await methods.transfer(r1, 300).send({ from: sender });
  await mineBlocks(web3, 15);
  // 检查旧状态已被丢弃
  await this.indexer.processNewBlocks();
  const newBalances = this.indexer.getBalances();
  expect(newBalances[sender]).to.be.bignumber.eq("700");
  expect(newBalances[r1]).to.be.bignumber.eq("300");
  expect(newBalances[r2]).to.be.bignumber.eq("0");
});
```

### 注

这些测试技术不仅可以用来测试与智能合约交互的组件，也可以用来测试智能合约本身。为你部署到网络上的任何代码提供良好的测试覆盖率是一种良好实践，尤其是考虑到你之后（在大多数情况下^(⁸⁹)）将无法修改它来修复任何错误。

### 关于中心化的说明

本书中，我们首次在应用程序中引入了服务器端组件。我们现在不再仅仅构建一个直接连接到区块链的纯客户端应用，而是有了一个应用运行所必需的新的中心化依赖项。可以说，我们的应用因此不再符合去中心化应用的标准，因为它盲目地信任由单个团队运行的服务器返回的数据。

根据被查询数据的类型，可以通过让客户端*验证*服务器提供的部分数据来缓解这个问题。沿用ERC20余额的例子，客户端可以从索引服务器请求某个代币的top 10持有者，然后对照区块链验证他们的余额是否正确。他们可能仍然不是top 10持有者——但至少客户端可以保证余额未被篡改。

然而，这并非问题的解决方案，因为并非所有数据都能在不重新查询所有事件的情况下对照区块链进行验证。此外，我们的应用现在依赖于一个可能随时宕机的组件，导致应用无法使用。

让我们讨论解决这个问题的两种不同方法。接下来的讨论不仅适用于索引，也适用于任何其他链下服务，例如存储或密集型计算。

#### 去中心化服务

解决这个问题的一种方法是寻找*去中心化*的链下解决方案。例如，在撰写本文时，一个名为*EthQL*^(⁹⁰)的、面向区块链数据的GraphQL接口正在审查中。这可以作为所有以太坊节点标准接口的一部分添加，从而允许任何客户端更方便地查询事件。另一个例子是`thegraph`^(⁹¹)项目，它为不同的协议提供定制的GraphQL模式。它们依赖一个由代币激励的去中心化节点网络来保持这些模式的最新状态，并回答用户的任何查询。

虽然这些去中心化解决方案很优雅，但它们可能尚未准备好适用于所有用例。去中心化的索引或计算解决方案仍在设计中。即使准备就绪，通用的去中心化解决方案也可能无法始终满足你应用的特殊需求。考虑到这一点，我们将讨论第二种方法。

#### 中心化应用

一个明显的“非解决方案”就是接受应用程序*可以是中心化的*。这听起来可能是一个有争议的说法，因为本书一直重点强调去中心化应用，但情况未必如此。

可以说，基于区块链的系统的优势不在于应用层，而在于协议层。通过依赖链作为最终真相来源，并构建在其上运行的开源协议，任何开发者都可以自由地构建应用来与这些协议交互。这赋予了用户灵活性，可以在充当通往公共去中心化协议层中数据*网关*的不同应用之间切换。因此，去中心化可以被理解为：能够在任何时候打包数据并离开，同时保留所有数据以及由共享去中心化层产生的网络效应。

这种理念赋予了我们作为开发者自由：在应用层，通过利用任意数量的中心化组件（用于查询、存储、计算或我们可能需要的任何其他服务），来构建我们想要的强大解决方案。这些解决方案有潜力提供比完全去中心化解决方案更丰富的用户体验。

正如你所想象的，中心化是以太坊开发者社区中一个有争议的问题。对于这个话题没有绝对正确或错误的答案，讨论可能会随着时间的推移而不断发展。无论你采取哪种方法，请确保理解其利弊，并根据你正在构建的解决方案的需求进行权衡。

### 存储

现在我们将关注以太坊中的另一个问题：存储。将以太坊区块链上的数据存储非常昂贵，每字节花费625 gas（向上取整到32字节的插槽），此外，仅仅将数据发送到区块链就需要为每个非零字节支付基础的68 gas。以1 GWei的Gas价格计算，存储一个100 KB的PNG图像将花费约0.07 ETH。另一方面，将数据保存在日志中供链下访问要便宜得多，每字节花费8个gas单位，外加发送数据的基础68 gas（对于我们示例图像的成本来说，这相当于其1/10），但随着我们开始扩展规模，它仍然很昂贵。这意味着我们需要寻找替代方案来存储大型应用数据。

#### 链下存储

遵循与我们用于索引类似的方法，我们可以设置一个独立的中心化服务器来提供存储能力。我们可以存储用于从链下检索该数据的URL，而不是将实际数据存储在区块链上。当然，这种方法仅适用于合约执行逻辑不需要而仅用于链下目的的数据——例如显示与某个资产关联的图像。

与URL一起，我们还应该存储所存储数据的哈希值。这允许任何检索该信息的客户端验证存储服务器是否篡改了数据。即使由于存储服务器宕机导致数据无法访问，哈希值也保证了任何客户端在数据可用时都可以检查其正确性。换句话说，**我们可能通过将数据迁移到链下来牺牲其可用性，但不会牺牲其完整性**。

我们将以上一章中的ERC721铸造应用作为示例用例，来说明如何实现这一点，并将与每个代币关联的元数据（例如名称和描述）存储在链下站点上。

### ERC721 元数据扩展

在进入具体实现之前，我们先简要回顾一下 ERC721 标准的一个扩展：元数据扩展。该扩展规定每个代币都关联一个 `token URI`，其中包含该代币的元数据。这个 URI（可以是 HTTP URL）保存了一个 JSON 文档，其中包含每个代币的信息。元数据可以包括规范名称、描述文本、标签、作者信息、图像，或任何其他与收藏品领域相关的字段。

```solidity
function tokenURI(uint256 tokenId)
external view returns (string memory);
```

我们将扩展 ERC721 合约，集成由 `openzeppelin-solidity@2.2.0` 包提供的 `ERC721Metadata` 基础合约，该合约会追踪每个代币的 URI。

```solidity
// 02-storage/contracts/ERC721PayPerMint.sol
pragma solidity ⁰.5.0;
// 引入 SafeMath, ERC721, ERC721Enumerable, ERC721Metadata
contract ERC721PayPerMint
is ERC721, ERC721Enumerable, ERC721Metadata {
using SafeMath for uint256;
constructor() public ERC721Metadata("PayPerMint", "PPM") { }
function exists(uint256 id) public view returns (bool) {
return _exists(id);
}
function mint(
address to, uint256 tokenId, string memory tokenURI
) public payable returns (bool) {
require(msg.value >= tokenId.mul(1e12));
_mint(to, tokenId);
_setTokenURI(tokenId, tokenURI);
return true;
}
}
```

现在让我们修改应用，将元数据保存到存储服务器，并将包含数据的 URL 以及内容哈希一同添加到代币合约中。

### 保存代币元数据

我们将修改 ERC721 组件中的主要 `mint` 方法，使其不仅接受 ID，还接受标题和描述字符串字段。我们会将这些数据保存到链下，获取一个 URL，并将其传递给接下来的合约 `mint` 调用。

```javascript
// 02-storage/src/components/ERC721.js
async mint({ id, title, description }) {
const { contract, owner } = this.props;
const data = JSON.stringify({ id, title, description });
const url = await save(data);
const value = new BigNumber(id).shiftedBy(12).toString(10);
const gasPrice = await getGasPrice();
const gas = await contract.methods
.mint(owner, id, url).estimateGas({ value, from: owner });
contract.methods.mint(owner, id, url)
.send({ value, gas, gasPrice, from: owner })
.on('transactionHash', () => {
this.addToken(id, { title, description });
})
}
```

`save` 函数将计算数据的 `hash`，将其用作标识符，并存储到存储服务器中。请注意，通过使用哈希值作为标识符，任何客户端都可以验证检索到的数据是否被篡改过。

```javascript
// 02-storage/src/storage/local.js
import { createHash } from 'crypto';
const server = 'http://localhost:3010';
export async function save(data) {
let hash = createHash('sha256').update(data).digest('hex');
let url = `${server}/${hash}`;
await fetch(url, {
method: 'POST', mode: 'cors', body: data,
headers: { "Content-Type": "application/json" }
});
return url;
}
```

在这个例子中，接收 `POST` 请求的服务器是一个 `NodeJS` 进程，它会在一个 `URL` 路径下接受任意 `JSON` 数据，将其本地保存为文件，并在收到 `GET` 请求时提供该数据。在实际应用中，你可能希望依赖真正的存储服务。

现在，我们可以在初始加载时加载代币的元数据。我们将在主组件加载时以及获取到现有代币列表后，添加对新函数 `loadTokensData` 的调用。

```javascript
// 02-storage/src/components/ERC721.js
loadTokensData(tokens) {
const { contract } = this.props;
tokens.forEach(async ({ id }) => {
// 从合约中获取元数据 URL
const url = await contract.methods.tokenURI(id).call();
// 从 URL 中获取数据
const data = await fetch(url)
.then(res => res.json()).catch(() => "");
// 验证数据完整性并更新状态
const hash = createHash('sha256')
.update(JSON.stringify(data)).digest('hex');
const path = new URL(url).pathname.slice(1);
if (path === hash) this.setTokenData(id, data);
});
}
```

请注意，元数据的完整性是通过在接受数据前计算其哈希值来验证的。你可以通过修改本地文件系统中已保存的元数据（查看项目 `server/data` 文件夹），并检查修改后的代币是否不再显示元数据，来测试这一点。

这使得我们在铸造每个非同质化代币时，可以为其关联额外的数据。任何展示代币信息的应用都可以实际利用这些数据。从这个角度来看，你可以将代币元数据视为等同于常规 `HTML` 页面中的 `opengraph` 元数据。

### 星际存储

作为中心化存储方案的替代，我们可以将数据存储在 `星际文件系统`（`IPFS`）中。`IPFS` 是一个“用于存储和访问文件、网站、应用和数据的分布式系统”。换句话说，它充当了一个去中心化的存储系统。

#### 什么是 IPFS？

`IPFS` 作为一个点对点的内容分发系统运行。任何节点都可以加入网络，无论是专用的 `IPFS` 服务器，还是普通用户的家用计算机。当用户从网络请求一个文件时，该文件会从距离最近且拥有该文件的节点下载，并且该文件也会从这个新位置提供给其他用户下载。内容的可用性取决于是否有足够多的用户愿意存储相关文件。

与大多数传统文件系统不同，`IPFS` 中的任何数据单元都不是通过其位置来标识的，而是通过其`内容`来标识的。当从 `IPFS` 网络请求文件时，你需要通过其标识符（也就是`内容哈希`）来定位它。这保证了所有内容的完整性，因为客户端会对照标识符验证接收到的所有内容。这也允许客户端请求文件时，无需知道它在网络中的`存储位置`。

这意味着 `IPFS` 中的内容是不可变的。任何对内容的修改都需要在新标识符下存储一个全新的副本。只要有人保留副本，网络中就会保留之前的文件。

所有这些属性使得 `IPFS` 成为区块链应用的绝佳选择。我们在上一节中手动构建的内容哈希验证，该协议本身就已经提供了。通过将`内容标识符`（而非其位置）索引到智能合约中，我们可以将区块链数据与任何中心化内容提供商解耦。

### 注意

`IPFS` 依赖于用户愿意保存和共享内容以保证可用性，这可能会使其看起来不适合构建关键应用。尽管如此，你可以通过依赖 `IPFS` 固定服务来保证应用的数据可用性。这些服务扮演着传统存储服务器的角色，但它们通过向所有用户提供`你的`内容（当然，这是要收费的）来参与 `IPFS` 网络。

好的，作为一名高级文档工程师和翻译员，我将严格遵循您的注意事项和示例，将给定的英文文本翻译成中文。

#### 在我们的应用中使用 IPFS

为了在我们的应用中启用 `IPFS` 支持，我们首先需要连接到一个 `IPFS` 节点。这类似于连接到以太坊节点以访问以太坊网络，不同之处在于，我们不需要私钥或加密货币就能向 `IPFS` 写入任何数据。

我们可以选择在自己的应用中托管一个 `IPFS` 公共节点，或者依赖第三方提供商。例如，`Infura` 不仅提供以太坊公共节点，也提供 `IPFS` 节点，这意味着我们可以直接使用它们的 `IPFS` 网关。

然而，用户也可能在自己的计算机上运行他们自己的 `IPFS` 节点。`IPFS` 提供了一个浏览器扩展，即 `IPFS Companion`，^(⁹⁵)，它可以连接到本地节点，使浏览器具备 `IPFS` 功能。这包括为 `ipfs` 链接添加支持，并在所有站点向全局 `window` 对象注入一个 `ipfs` 对象——这与 `Metamask` 向所有站点添加 `ethereum` 提供者对象的方式非常相似。

### 注意

还有一种选择是在你的网站内部运行一个 `IPFS` 节点。`js-ipfs` 库提供了整个 `IPFS` 协议的浏览器兼容实现，因此你可以在用户访问你的应用时启动一个 `IPFS` 守护进程。然而，这可能会使你的应用程序变得非常臃肿，并且应用内 `IPFS` 进程不如专用进程稳定。因此，建议与网络交互的方法是使用 `IPFS HTTP API` 连接到一个独立的节点。

在我们的应用中打开一个 `IPFS` 连接与打开一个以太坊连接非常相似：我们首先检查是否存在一个全局的连接对象，如果没有，则回退到一个知名的公共节点（列表 6-25）。我们将使用 `ipfs-http-client@30.1` JavaScript 库来访问网络，它是与 `web3.js` 对等的 `IPFS` 库。

```javascript
// 02-storage/src/storage/ipfs.js
import ipfsClient from 'ipfs-http-client';
async function getClient() {
  if (window.ipfs && window.ipfs.enable) {
    return await window.ipfs.enable({
      commands: ['id', 'version', 'add', 'get']
    });
  } else {
    return ipfsClient({
      host: 'ipfs.infura.io', port: '5001',
      protocol: 'https', 'api-path': '/api/v0/'
    });
  }
}
```

*列表 6-25*
*创建一个新的 `ipfs` 客户端实例。我们首先检查由 Companion 扩展注入的全局对象是否可用。如果没有，我们回退到连接到 Infura IPFS 网关。*

使用这个新的 `IPFS` 客户端，我们现在可以轻松地将代币元数据保存到 `IPFS` 网络，而不是集中式服务器。

```javascript
export async function save(data) {
  const ipfs = await getClient();
  const [result] = await ipfs.add(Buffer.from(data));
  return `/ipfs/${result.path}`;
}
```

根据 `URL` 从 `IPFS` 网络中取回结果也同样简单。注意，我们不再需要验证其完整性，因为协议会自动处理这一点。

```javascript
export async function load(url) {
  const ipfs = await getClient();
  const [result] = await ipfs.get(url);
  return JSON.parse(result.content.toString());
}
```

#### 在 IPFS 上托管我们的应用

`IPFS` 不仅可以用来存储我们的应用数据，还可以用来存储应用程序本身，以实现最大程度的去中心化。我们应用程序的所有客户端代码都可以上传到 `IPFS` 并从那里提供服务。但是，我们的用户如何从常规浏览器访问它呢？或者如何在不指定哈希值的情况下对其进行寻址？

第一个问题可以通过 `IPFS` 网关来解决（参见列表 6-26 的示例）。`IPFS` 网关是一个常规的网站，它从 `IPFS` 网络提供内容。它允许你通过路径 `/ipfs/CID` 访问任何 `IPFS` 项目，其中 `CID` 是标识网络上每个对象的内容哈希值。

```
https://gateway.ipfs.io/ipfs/QmeYYwD4y4DgVVdAzhT7wW5vrvmbKPQj8wcV2pAzjbj886/
https://ipfs.infura.io/ipfs/QmeYYwD4y4DgVVdAzhT7wW5vrvmbKPQj8wcV2pAzjbj886/
https://cloudflare-ipfs.com/ipfs/QmeYYwD4y4DgVVdAzhT7wW5vrvmbKPQj8wcV2pAzjbj886/
```

*列表 6-26*
*你可以通过列出的任何一个公共网关，直接在浏览器上访问以下地址，查看 `ipfs.io` 网站的一个旧版本。由于内容的 `ID` 是其哈希值，因此你可以确信所有网关都将提供完全相同的对象。*

第二个问题，为 `IPFS` 网站提供用户友好的名称，可以使用 `DNSLink` 来解决。`DNSLink` 是一种使用 `DNS TXT` 记录将 `DNS` 名称映射到 `IPFS` 内容的过程。

假设我们想将我们在 `IPFS` 上的网站映射到域名 `example.com`。通过向 `_dnslink.example.com` 添加一个值为 `dnslink=/ipfs/CID` 的 `TXT` 记录，任何 `IPFS` 网关都会自动将对 `/ipfs/example.com` 的请求映射到指定的内容。

不仅如此，我们还可以为我们的域名指定一个指向网关的 `CNAME DNS` 记录。这使得我们可以直接从 `IPFS` 指定的网关在 `example.com` 自动提供我们的页面服务。^(⁹⁶) 总而言之，访问我们网站的完整流程如下：

*   用户向 `example.com` 发起请求。
*   `DNS` 查询返回一个指向 `gateway.ipfs.io` 的 `CNAME` 记录。
*   用户使用 `example.com` 作为 `Host` 头部，向 `gateway.ipfs.io` 的 `IP` 地址发送请求。
*   网关同时对 `example.com` 和 `_dnslink.example.com` 进行 `DNS TXT` 查询，并获取一个 `IPFS CID` 作为响应。
*   网关透明地将 `IPFS` 中的内容提供给最终用户。

通过依赖 `IPNS`（星际名称系统），可以引入另一个间接层级。该系统允许你拥有指向 `IPFS` 内容的可变链接，尽管 `IPNS` 链接也是哈希值。然后，你可以让你的 `DNSLink` 指向你的 `IPNS` 名称，而不是 `IPFS ID`，并通过仅将 `IPNS` 链接更新到你内容的新版本来更新你的网站。这样，你就不必在每次部署新版本网站时修改 `DNS TXT` 记录。

### 总结

我们已探讨了构建非简单以太坊应用时出现的两个问题：如何对链上数据执行复杂查询，以及如何存储大量数据。这两个问题都需要跳出以太坊本身，依赖其他服务——无论是中心化还是去中心化的服务。我们还研究了如何编写与以太坊网络交互的单元测试，以及处理链重组的一些策略。

除了本章详述的具体问题或策略外，或许最重要的收获是明确应用的`去中心化`需求。虽然我们习惯于传统的非功能性需求，如性能、安全性或可用性，但区块链应用也需要考虑去中心化。去中心化是首先使用区块链的核心原因，因此特别关注它是合理的。

与其他非功能性需求一样，去中心化并非二元对立。我们的应用可以拥有不同程度的去中心化，这取决于技术栈的哪些组件是中心化的，我们对商业第三方（相对于点对点网络）的信任程度，或者用户对自己数据的控制程度。

例如，一个金融应用除了管理用户资产的底层协议外，可以完全中心化，从而实现高性能和良好的用户体验，同时确保用户能够随时处置自己的资产。另一方面，一个专注于绕过审查的应用可能需要完全去中心化，以避免被其托管提供商关闭的风险。

不同的应用会有不同的需求。明确自己的需求至关重要，这样你才能知道可以使用哪些解决方案，并据此构建应用的架构。

脚注 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16