# 4. 查询网络

在关于编写智能合约的“不那么简短”的插曲之后，我们将回顾连接到以太坊网络以检索数据的不同方式。我们将涵盖不同的连接方法以及监听变化的模式，并在一个用于监控 `ERC20` 通证转账的示例应用中将其整合在一起。

## 连接到网络

从网络检索数据的第一步，实际上是连接到一个以太坊节点。由于 Web 应用不直接连接到网络，它们需要依赖一个节点来回答关于区块链状态的任何查询。我们将从回顾节点类型、连接方法和 Provider 对象开始。

### 关于全节点和轻节点

一个典型的以太坊节点是 `Geth` 或 `Parity` 实例⁵⁹，它拥有区块链的（部分或全部）副本，能够回答来自客户端（例如 DApp）的查询，并转发交易（更多内容将在下一章介绍）。拥有完整区块链副本的节点称为*全节点*。这些节点要么拥有区块链历史中的所有数据，要么可以重新计算这些数据。大多数客户端默认以这种模式运行。

全节点也可能存储所有历史数据。这些节点被称为*归档节点*，由于支持它们需要大量的磁盘空间——在撰写本文时接近 2TB——因此它们要稀少得多。当你需要查询旧区块中的特定信息，例如一年前某个合约的状态或某个账户的余额时，就需要用到归档节点。

作为全节点的替代方案，一些节点可能以*轻客户端*模式运行。这些节点只保存区块头，并按需从网络请求信息。它们运行起来比全节点轻量得多，因此适合移动设备，但作为 DApp 的后端则不是很好的选择，因为查询的解析时间会更长。

#### Infura 和公共节点

关于节点的下一个问题是，哪些节点可供我们的应用程序使用。在理想的去中心化场景中，每个用户都应该运行自己的完整以太坊节点，以便自行验证所有交易，并避免信任第三方。移动设备或物联网设备上的用户可能会选择运行轻节点，这些节点会信任其他节点来转发信息，但无论如何仍会进行验证。

在当前的格局下，我们用户中只有一小部分会真正运行以太坊节点。大多数用户还只是在学习以太坊是什么，并想知道如何购买他们的第一个 ETH 来支付燃料费，以启动他们的初始交易。让他们运行自己的节点目前还不可行。

因此，为了帮助简化以太坊的采用过程，有许多*公共节点*可用。当一个以太坊节点不持有私钥、向公众开放、并用于回答区块链查询和转发预签名交易时，它就被称为公共节点。

特别地，**Infura**（日语中“基础设施”的意思）是一项服务，它为以太坊主网以及 Kovan、Ropsten 和 Rinkeby 测试网提供 HTTP 和 WebSocket 端点，连接到公共全节点。由于其可靠性以及免费使用的特性，它被许多去中心化应用和钱包广泛使用。

### JSON-RPC 接口

所有以太坊节点，无论其具体实现如何，都公开一组众所周知的方法，这些方法构成了 *JSON-RPC 接口*。顾名思义，这是一个基于 JSON 的 API，用于执行远程过程调用，并构成了客户端与节点交互的底层接口。常见的方法包括 `call`、`sendTransaction`、`getBlockByNumber`、`accounts` 或 `getBalance`。甚至还有用于查询节点本身状态的方法，例如其是否正在同步或连接了多少个对等节点。

### 注意

鉴于它是一个底层接口，你不太可能需要手动构建 JSON-RPC 调用。大多数库（例如 `web3.js` 或 `ethers.js`）会替你生成调用并提供响应。不过，了解底层的工作原理总是有好处的，以防你遇到令人头疼的抽象泄漏问题。

值得一提的是，某些节点可能没有实现所有方法。例如，Infura HTTP 端点不提供诸如 `newFilter`（本章稍后将详细介绍过滤器）之类的开销较大的操作。当我们在讨论如何将应用连接到以太坊网络时，记住这一点很重要。



### 连接协议

有三种不同的协议可用作交换 JSON-RPC 消息的传输层。节点可以被配置为处理其中任何一种。

**HTTP** 协议是最简单的一种。它提供了一个基于 HTTP 的简单接口，用于 POST JSON 消息。某些节点可能设置在 HTTPS 加密连接之后，并且可能需要基本身份验证才能访问。一个简单的 HTTPS 连接字符串示例如下：

```
"https://user:password@example.com:8545/"
```

一个更有趣的选择是 **websocket** 协议。websocket 连接是客户端和服务器之间持久的双向连接。这不仅允许客户端执行所有可用的 JSON-RPC 调用，还可以订阅从节点推送到客户端的变更（稍后会详细介绍）。与 HTTP 连接一样，websocket 也可以建立在 SSL 之上，并且可能包含基本身份验证：

```
"wss://user:password@example.com:8545/ws"
```

最后，**IPC**（进程间通信）协议基于节点创建的本地 UNIX 域套接字。有权访问此套接字的客户端可以通过其文件名连接到它。这些连接旨在供能够访问与节点相同文件系统的进程使用，因此不用于 Web 应用程序。

```
"ipc://home/ubuntu/.ethereum/geth.ipc"
```

### 替代 API

作为建立连接到节点 JSON-RPC 接口的替代方案，你可以选择从其他来源查询区块链数据。

**Etherscan** (etherscan.io) 是一项集中式服务，它不仅提供一个基于 Web 的区块链浏览器，你可以在其中直观地检查发送到账户或从账户发送的所有交易，而且还提供一个纯 HTTP API（清单 4-1），该 API 实现了 JSON-RPC 接口中存在的许多方法。

```
#### Etherscan API
curl "https://api.etherscan.io/api
?module=proxy
&action=eth_getTransactionCount
&address=$ADDRESS
&tag=latest
&apikey=YourApiKeyToken"
#### 常规 JSON-RPC 调用
{"jsonrpc":"2.0"
,"method":"eth_getTransactionCount"
,"params":["$ADDRESS","latest"]
,"id":1}
清单 4-1
向 Etherscan API（上文）执行 getTransactionCount 调用与标准 JSON-RPC 调用（下文）的示例。两者都返回相同的 JSON 对象作为响应
```

某些 JavaScript 库，例如 `ethers.js`，甚至包含抽象了到 Etherscan API 连接的 *provider* 对象，因此它可以像任何其他标准 JSON-RPC 连接一样无缝使用。现在让我们来谈谈提供者的角色。

### 注意
目前我们不深入讨论特定领域的 API。一个项目可能决定提供一个 API 来查询其领域的相关数据。你也可以选择设置一个集中式服务器，该服务器汇总来自你的协议的区块链数据，并将其中继到客户端应用程序。

### Provide 对象

正如我们在构建第一个示例 DApp 时在第 2 章中简要看到的那样，与节点的连接由一个 javascript *provider* 对象管理。提供者的责任是抽象所使用的连接协议，并为发送 JSON-RPC 消息和订阅通知提供一个最小接口。

### 注意
在撰写本文时，来自不同库的提供者具有略有不同的 API。目前正在努力将最小提供者标准化为 EIP 1193，但仍处于草案阶段。

例如，`web3` JavaScript 库^(⁶⁰) 提供了以下用于连接到 HTTP、websocket 或 IPC 接口的提供者（清单 4-2）。然后使用该提供者来初始化完整的 `web3` 对象的实例。

```
const Web3 = require('web3');
const httpProvider = new
Web3.providers.HttpProvider("https://example.com");
const wsProvider = new
Web3.providers.WebsocketProvider("wss://example.com");
const ipcProvider = new
Web3.providers.IpcProvider("/home/ubuntu/.ethereum/geth.ipc");
const web3 = new Web3(provider);
清单 4-2
用于创建提供者并初始化 web3 实例的 web3@1.2.0 代码示例
```

只有在需要手动设置与节点的连接时，才需要创建提供者实例。在大多数情况下，你实际上会将此责任委托给用户的启用了 web3 的浏览器。

#### Metamask 和启用了 Web3 的浏览器

在第 2 章之后，你现在应该熟悉 Metamask，它是一个浏览器扩展，充当 Web 应用程序和以太坊网络的桥梁。还有其他选项，例如适用于 Android 的 Cipher 或 Opera 浏览器，尽管本书将重点介绍 Metamask，因为它是目前最广泛使用的工具。

启用了 Web3 的浏览器通过将 *provider* 实例注入到全局作用域中来工作。这个提供者如何工作或其底层机制对你的 DApp 来说并不重要。DApp 应该能够查询所需的任何信息，并让提供者解析它。

请注意，可能需要 *启用* 此提供者才能访问用户的账户或请求签署交易（清单 4-3），这将提示用户接受来自 DApp 的请求以访问其账户信息。

```
// Metamask 将 web3 提供者注入为 window.ethereum
const Web3 = require('web3');
const provider = window.ethereum;
if (provider) {
try {
// 请求访问查询用户账户的权限
await provider.enable();
} catch (error) {
// 用户拒绝了账户访问，但我们仍然可以
// 运行网络查询
}
const web3 = new Web3(provider);
}
清单 4-3
使用 Metamask 注入的提供者实例化 web3 对象的代码片段
```

Metamask 默认通过 HTTPS 连接到 Infura 公共服务器。这使得任何下载了该扩展的用户都可以立即拥有一个与以太坊网络的连接并运行，而无需维护和同步自己的节点。尽管如此，Metamask 也允许高级用户建立自定义连接到其他节点，例如他们自己的节点（图 4-1 和 4-2）。

![../images/476252_1_En_4_Chapter/476252_1_En_4_Fig2_HTML.jpg](img/476252_1_En_4_Fig2_HTML.jpg)

图 4-2
Metamask 用于选择要连接的节点的控件，在点击扩展对话框顶部的网络下拉菜单时显示。前四个是连接到 Infura 托管的公共节点

![../images/476252_1_En_4_Chapter/476252_1_En_4_Fig1_HTML.jpg](img/476252_1_En_4_Fig1_HTML.jpg)

图 4-1
Metamask 设置选项卡允许用户配置他们自己的节点连接

#### 子提供者

某些 web3 提供者也可能由 *子提供者* 组成。子提供者是一种非标准对象，它会拦截通过提供者进行的调用。除其他用途外，子提供者通过填补所使用的以太坊节点功能集中的任何空白来帮助提供通用接口。从这个意义上说，子提供者充当隐藏在提供者内部的 polyfill。

例如，连接到一个不提供 *filters* API（用于轮询特定更改）的节点的提供者，可能包含一个模拟该客户端功能的 *filter subprovider*。Metamask 注入的 web3 提供者就是这种情况：由于 Infura 不提供 filters API，Metamask 通过自定义子提供者在提供者级别添加了该功能。这样，作为开发人员，你无需担心支持哪些 API，并且无论回答你查询的是哪个节点，你都会得到一个一致的接口。

我们将在下一章重新讨论子提供者，届时我们将讨论提供者和签名者，因为 Metamask 将其签名者实现为另一个子提供者。



### 选择合适的连接方式

到目前为止，我们回顾了不同类型的节点（全节点和轻节点、公共节点和私有节点），以及不同的连接协议（`ipc`、`http` 和 `websockets`）。我们还学习了如何设置 `provider` 对象，以及如何启用由支持 `web3` 的浏览器注入的 provider。鉴于所有这些选项，自然引出一个问题：在 *从 DApp 查询信息* 时，我们应该选择哪种连接方式。

#### 尊重用户的选择

首先，如果我们的用户使用的是支持 `web3` 的浏览器，那么 DApp 应当依赖该浏览器注入的 provider。支持 `web3` 的浏览器意味着用户已经是以太坊生态系统的一部分，并且可能正在运行自己的节点。因此，我们需要为用户提供一种方式，让他们在浏览我们的 DApp 时选择想要使用的节点。

虽然我们可以重新实现 Metamask 的网络连接选择界面，但这样做意义不大。希望连接到其他节点的用户会已经运行了 Metamask 或其他支持 `web3` 的浏览器，并且已经预先配置好了自己的节点。因此，注入的 `web3` provider 应始终是我们连接网络的首选。

请记住，需要启用 provider 才能访问用户账户列表。不过，如果应用程序不需要此信息，则可以跳过这一步。

#### 使用公共节点

下一个选择很简单：连接到公共节点。你可以为自己的 DApp 搭建自己的节点，也可以使用 Infura 提供的节点。使用自己的节点具有部署自有基础设施的所有优缺点：你不依赖于第三方，但需要关注节点的运行状况。请记住，没有任何机制能阻止任意数量的用户连接到你的节点，因此你应当为流量激增做好准备。正因如此，依赖外部基础设施提供商可能会更简单。

作为 Infura 的替代方案，你也可以依赖像 Etherscan 这样的公共 API。作为 `web3.js` 的替代方案，`Ethers.js` 默认连接到 Infura，并在连接失败时回退到 Etherscan。

请注意，在所有情况下，如果你的 DApp 依赖第三方，它就是在依赖外部的中心化服务来从区块链获取数据。由于 DApp 的强项之一正是去中心化，添加一个需要被信任的组件可能在这一方向上是一种倒退。你需要自行决定，为 DApp 用户权衡便利性与去中心化之间的关系。因此，一个好的经验法则是：如果找到注入的 provider 则使用它，否则回退到中心化服务。

#### 整合方案

清单 4-4 中的代码尝试从现代和旧版两种 `web3` 浏览器加载注入的 provider。如果加载失败，则回退到使用 Infura 的安全 `websocket` 端点。该 provider 用于创建 `web3.js` 实例，但同样的代码也可用于其他库。

```javascript
async function getWeb3() {
  // 现代 web3 浏览器
  if (window.ethereum) {
    const web3 = new Web3(window.ethereum);
    // 仅在需要访问用户账户时
    try {
      await window.ethereum.enable();
    } catch (error) {
      console.error("无法访问用户账户");
    }
    return web3;
  }
  // 旧版 web3 浏览器
  else if (window.web3) {
    return new Web3(window.web3.currentProvider);
  }
  // 标准浏览器
  else {
    return new Web3("wss://mainnet.infura.io/ws/v3/TOKEN");
  }
}
```
*清单 4-4 基于 metamask.io 提供的代码，用于初始化 DApp 的 web3 连接的代码片段*

## 检索数据

现在我们已经知道如何连接到网络，可以开始实际检索数据了。我们将回顾如何访问网络信息、账户余额、执行静态调用以及订阅事件。和之前一样，我们将使用 `web3@1.2.0` 作为与以太坊网络交互的库，但其他库也应提供类似功能。

### 网络信息

我们可以从查询通用网络信息开始。首先，始终检查你是否连接到了预期的网络，这是一个好习惯。如果你的应用程序是为在 Rinkeby 测试网络上使用而设计的，你不希望用户意外地使用了与主网的连接。为此，你可以获取当前所连接网络的标识符，并将其与预期网络的标识符进行比较。

```
> await web3.eth.net.getId()
```

网络由数字标识符来标识。主网是 1，Ropsten 是 3，Rinkeby 是 4，Kovan 是 42。临时的开发网络通常使用更高的 ID 进行设置。

### 备注

与 JavaScript 中对外部数据源的大多数请求一样，对以太坊网络的调用是异步操作。不同的库可能以不同的方式处理这个问题，要么使用回调函数，要么返回 promise。具体来说，`web3.js` 既支持传统的错误优先回调，也支持 promi-event。Promi-event 是兼具事件发射器功能的 promise 对象，允许你监听异步操作的不同阶段。它们在下一章中将变得更加重要。现在，我们将简单地使用 `async-await` 语法来处理 promise。

我们可以从网络中获取的另一条信息是当前区块号。轮询这个值可以让我们知道何时有新区块被添加到链上，其中可能包含修改我们正在使用的合约状态的交易，从而触发我们在应用中重新读取数据。

```
> await web3.eth.getBlockNumber()
```

我们还可以获取一个区块的详细信息，例如其哈希值、使用的总 gas、其矿工以及其中包含的所有交易列表。请注意，我们可以通过区块号、哈希值或字符串 `latest` 来引用一个区块，以表示我们想要链上的最新区块。

```
> await web3.eth.getBlock('latest')
{ author: '0xea674fdde...',
  gasLimit: 8000029,
  gasUsed: 1808896,
  hash: '0xcdb2699b240ece675611aa...',
  number: 7059810,
  transactions:
   [ '0xca7d315abc76988ddcfa49...',
     '0x9b72090bbabe017d4bcf5b...',
     '0xa50150e448a0cc40a29986...',
     ... ],
  ... }
```

我们还可以获取关于节点本身而非网络的信息。例如，我们可以查询节点运行的软件版本，甚至可以在该版本存在已知问题时警告用户。

```
> await web3.eth.getNodeInfo()
'Parity-Ethereum//v2.1.11-stable-e9194b0-20190107/x86_64-linux-gnu/rustc1.31.1'
```

另一个可能有用的是检查节点是否与链的其余部分保持同步。刚搭建好的节点可能尚未完成同步，因此它们无法返回来自网络的近期信息。如果节点不再同步，你就可以安全地依赖它。

```
> await web3.eth.isSyncing()
false
```

你还可以从节点查询更多信息。请务必查看 `web3.js` 参考文档^(⁶²)以获取更多方法。



### 账户余额与代码

给定一个地址，你可以查询该账户存储的 ETH，无论它是外部账户还是合约。此外，由于区块链历史不可篡改，你甚至可以查询该账户在更早时间点的余额（代码清单 4-5）。你能回溯的区块数量取决于你连接的是归档节点还是普通节点。

```
> const addr = '0xcafE1A77e84698c83CA8931F54A755176eF75f2C';
> const block = await web3.eth.getBlockNumber() - 10;
> await web3.eth.getBalance(addr, block);
'180300794957635301822239'
代码清单 4-5
查询十个区块前某地址的余额
```

请注意，ETH 余额**始终**以 Wei 为单位表示，这是 ETH 可分割的最小单位。1 ETH 等于 1e18 Wei（即 1 后面跟 18 个零）。你可以使用`web3`工具模块（代码清单 4-6)）进行单位转换。

```
> const balance = await web3.eth.getBalance(addr, block)
> web3.utils.fromWei(balance)
'180300.794957635301822239'
代码清单 4-6
使用 web3.utils.fromWei 从 Wei 转换为 ETH。反向方法为 toWei
```

你可能已注意到，在前面的代码片段中，ETH 余额返回的不是数字，而是**字符串**。这是为了避免在处理极大数值时丢失精度，因为 JavaScript 数字无法处理超大数量级。例如，在以下代码中，1822239 wei 在转换为整数时会丢失。

```
> parseInt(balance).toLocaleString();
'180,300,794,957,635,300,000,000'
```

这一设计决策是`web3.js`库特有的。其他库则依赖 JavaScript 的`bignumber`实现，例如`bignumber.js`^(⁶³)或`bn.js`^(⁶⁴)。一旦语言中原生`bignumbers`^(⁶⁵)的支持趋于稳定，库很可能会切换过去。无论如何，重要的是你要记住，以太坊中的大多数数字无法用常规 JavaScript 数字处理，否则你就有丢失精度的风险。

除了余额，你还可以获取地址上的代码，并用它来检查该地址是合约还是外部账户。你还可以检查代码本身，看它是否与已知合约的二进制代码匹配。

```
> await web3.eth.getCode(addr);
'0x6060604052361561011...'
```

请记住，这种检查账户是合约还是外部账户的方法远非可靠。如果你从一个地址获取不到代码，并不一定意味着它就是外部账户：合约可能会在稍后部署到该地址，或者合约可能已部署在那里但最终自毁了。总而言之，对于特别敏感的操作，你应该避免依赖任意地址是外部账户还是合约的判断。

### 调用合约

正如我们在第 2 章中看到的，你可以通过向其地址发出 JSON-RPC `call`来调用合约，从中查询信息。大多数合约都公开了 getter 函数，这些函数返回其当前状态的信息或执行纯计算；这些函数可以通过 Solidity 中带有`view`或`pure`修饰符来识别。与本章中列出的所有函数一样，调用它们不会消耗任何 gas，因为网络中任何节点都可以响应调用，而且不需要在区块链上引入更改。

这些调用可以使用`web3.js`中的`call`函数在底层执行，这需要手动提供目标地址和要发送到目标合约的原始数据。例如，`0x18160ddd`是用于访问 ERC20 代币合约`totalSupply`的函数选择器^(⁶⁶)，因此我们可以在主网上的现有合约（例如 BAT 代币）上测试它，该合约返回`1.5e27`的十六进制表示。

```
> const addr = '0x0d8775f648430679a709e98d2b0cb6250d2887ef';
> await web3.eth.call({ to: addr, data: '0x18160ddd' });
'0x00000000...0004d8c55aefb8c05b5c000000'
```

然而，我们通常会依赖`web3` Contract 抽象来与合约交互（代码清单 4-7）。如前所述，创建这样一个抽象需要合约的 ABI 及其地址。我们将使用 ERC20 的 ABI^(⁶⁷)来重复前面的示例。

```
> const abi = [
{
"constant": true,
"inputs": [],
"name": "totalSupply",
"outputs": [{"name": "", "type": "uint256"}],
"payable": false,
"stateMutability": "view",
"type": "function"
}, ...
];
> const erc20 = new web3.eth.Contract(abi, addr);
> await erc20.methods.totalSupply().call()
'1500000000000000000000000000'
代码清单 4-7
通过 web3 合约对象访问同一代币的总供应量。注意输出如何根据其类型进行格式化，而不是以原始十六进制值返回
```

### 备注

与`getBalance`一样，所有对合约的调用也可以包含一个可选的`block`参数，以防你需要查询合约在之前某个时间点的状态。请记住，请求链上太久远的区块的变更需要连接到归档节点，而归档节点并不总是可用。此外，还要注意，根据你的用例，谨慎的做法是只显示几十个区块前的数据，以保护自己免受可能的链重组影响。最近的数据通常总是可用的，无论节点是否保留归档。

合约对象还可以用于获取函数选择器，这些选择器可以插入到底层调用或原始交易中。在下面这行代码中，`encodeABI`方法返回了我们在本节开头使用的数据选择器。

```
> await erc20.methods.totalSupply().encodeABI()
'0x18160ddd'
```

合约还公开了一个便捷的接口，用于访问 ABI 上声明的所有事件（代码清单 4-8），使得查询某个区块范围内的所有事件变得容易。

```
> const block = await web3.eth.getBlockNumber();
> const opts = { fromBlock: block - 100, toBlock: block };
> await erc20.getPastEvents('Transfer', opts);
[{address: '0x0D8775F648430679A709E98d2b0Cb6250d2887EF',
blockNumber: 7060651,
logIndex: 91,
removed: false,
transactionHash: '0x3bd37...',
transactionIndex: 96,
transactionLogIndex: '0x0',
type: 'mined',
returnValues:
Result {
'0': '0xAAAAA6...',
'1': '0x664753...',
'2': '1905510325611397921584',
from: '0xAAAAA6...',
to: '0x664753...',
value: '1905510325611397921584' },
event: 'Transfer'
}, ... ]
代码清单 4-8
获取主网上 BAT 代币在过去 100 个区块内发生的转账事件。在此示例中，以 0xAAAAA6 开头的地址向地址 0x664753 转账了 1.9e21 个代币
```

每个日志对象都告知了事件发生的区块和交易，以及事件的名称（此处为`Transfer`），并包含了触发事件时使用的参数。

## 检测变更

现在我们将更深入地探讨事件。尽管我们已经知道如何查询过去的事件，但监听新事件是一种有用的方法，能在我们的应用中实时检测合约的变更。我们将看到三种不同的监控变更的方法。



### 轮询新区块

轮询是一种简单有效的响应变化的方法（代码清单 4-9）。由于以太坊网络中的任何变更都需要通过链上的新区块引入，因此一种完全有效的方法就是轮询新区块，并在新区块被挖出时重新读取你感兴趣的合约状态。由于以太坊区块每隔几秒就会生成一个，因此 1 秒的间隔就足够了。

```
let block = null,
totalSupply = null;
const interval = setInterval(async function() {
const newBlock = await web3.eth.getBlockNumber();
if (newBlock !== block) {
// 更新区块号
block = newBlock;
// 从合约中重新读取相关数据
totalSupply = await erc20.methods.totalSupply().call();
}
}, 1000);
代码清单 4-9
轮询新区块以更新 ERC20 合约的 totalSupply。虽然我们可以直接轮询 totalSupply，但如果需要在每个区块上更新的数据更多，这种方法会更高效
```

每当发现新区块时，你可以查询你的应用所交互的合约以获取其最新状态，并在有变更时相应地更新你的应用。另一种方法是在新区块上运行 `getPastEvents`，并且仅在存在影响你合约的事件时才做出响应。

### 安装事件过滤器

*事件过滤器*是以太坊节点提供的一种机制，用于检索匹配一组指定条件的新事件。它的工作原理是允许你在**节点上**安装一个事件过滤器，然后轮询匹配该过滤器的任何新事件。在 JSON-RPC 层面，此模式主要由以下方法支持：

- `newFilter` 用于在节点上安装新的事件过滤器，并返回一个过滤器 ID
- `getFilterChanges` 返回自上次调用此方法以来，给定过滤器 ID 的所有新日志
- `uninstallFilter` 用于根据 ID 移除过滤器

事件过滤器仍然依赖于轮询节点以获取新变更，但它们使用起来更方便，因为现在是由节点来跟踪需要发送给客户端的具体新事件。这省去了客户端需要定期调用 `getPastLogs` 来检查新事件的工作，并允许节点在需要时预先计算要发送的数据。此外，还可以为发送到节点的新区块和待处理交易安装过滤器。

### 警告

某些公共节点（例如 Infura 提供的节点）可能不支持安装事件过滤器。为了解决这个问题，Metamask 内置了一个 web3 子提供程序（subprovider），用于在客户端完全模拟过滤器的行为。这允许你在编写应用代码时使用事件过滤器，而无需担心你连接的节点是否真正支持它们。但是，请记住，在这种情况下，使用过滤器可能带来的性能提升将完全丧失。

现在，我们有必要探讨一下在检索和轮询事件时可以指定哪些选项。这些选项在创建新过滤器和获取历史日志时都可以使用：

- **区块范围**可用于指定要监视事件的区块。默认情况下，过滤器创建后会监视最新挖出的区块。
- 日志来源的一个或多个**地址**。从 web3 合约对象检索事件会自动将日志限制为合约实例的地址。
- 用于过滤事件的**主题（topics）**。回顾第 3 章，EVM 日志最多可以有四个索引主题（indexed topics）——这些主题用于在查询期间进行过滤。第一个主题总是事件选择器（event selector），而其余主题是 Solidity 中的索引参数（indexed arguments）。过滤器可以对任何主题施加限制，要求某个主题可选的匹配一组值。

例如，以下过滤器对象可用于检索最后 1000 个区块内发送给三个代币持有者组的所有 ERC20 代币转账。

```
> const block = await web3.eth.getBlockNumber();
> const filter = { to: [holder1, holder2, holder3] };
> const opts = { fromBlock: block - 1000, filter: filter };
> await erc20.getPastEvents('Transfer', opts);
```

web3 库本身不支持事件过滤器。相反，监视事件是通过第三种也是最后一种监听变更的机制来完成的：订阅。

### 创建订阅

监视事件的一种更高级选项是创建订阅。事件订阅的工作原理与事件过滤器类似，它们都是通过一组过滤器（区块范围、地址和主题）在节点上创建，以指示客户端对哪些事件感兴趣。然而，订阅不需要客户端轮询变更，而是依赖双向连接直接将新事件推送给客户端。因此，订阅仅适用于 websocket 或 IPC 连接，而不适用于 HTTP 连接。

### 注释

与事件过滤器不同，Infura 确实通过 URL `wss://mainnet.infura.io/ws/v3/PROJECTID` 支持 websocket 连接。尽管如此，如果用户通过常规 HTTP 连接选择自定义节点，Metamask 也附带了一个子提供程序，通过依赖轮询在客户端模拟订阅功能。同样，这允许你在应用中透明地使用事件订阅，如果连接或节点不支持该功能，子提供程序会进行 polyfill 填充。

在底层，当你监听事件时，web3 会使用订阅（代码清单 4-10）。这意味着只有在你通过 websocket 或 IPC 连接运行时，或者你有一个用于 polyfill 订阅的子提供程序时，才能依赖事件。当有匹配过滤器的新事件可用、发生错误或事件因区块链重组而被移除时，web3 事件发射器（event emitter）会进行报告。

```
> const filter = { to: [holder1, holder2, holder3] };
> const sub = erc20.events.Transfer({ filter })
.on('data', (evt) =>
console.log(`来自 ${evt.returnValues.from} 的新交易`)
)
.on('error', (err) =>
console.error(err)
)
.on('changed', (evt) =>
console.log(`已移除来自 ${evt.returnValues.from} 的交易`)
)
代码清单 4-10
设置订阅以监视 ERC20 代币合约上的 Transfer 事件。`data` 处理程序在每个新事件时触发，而 `error` 在订阅出错时触发。由于链重组而从链上移除的事件会在 `changed` 中触发。
```

当与服务器的连接关闭时，订阅会自动清除。或者，可以通过订阅对象上的 `unsubscribe()` 方法，或使用 `web3.eth.clearSubscriptions()` 来移除它们，后者会清除所有活动订阅。

与事件过滤器一样，可以为来自多个地址的事件、新的待处理交易或新区块设置订阅。使用后者，可以实现类似于轮询的模式，即安装一个订阅来监视新区块，并在每个区块上重新读取合约状态。然而，如果合约对所有状态变更都发出事件，那么监视这些事件效率要高得多。

## 示例应用

现在，我们将整合本章学到的所有内容，构建一个用于监视 ERC20 代币转账的 Web 应用程序。此应用仅从代币中检索数据，不提供任何用于实际发送交易的界面。

### 设置

我们将再次使用 `create-react-app` 包来引导我们的应用。不用说，使用此包并非必需，但它能简化我们的设置，并让我们专注于构建应用本身。

```
npm init react-app erc20-app
```



#### 依赖项

除了 `web3`，我们还将安装 `openzeppelin-solidity` 包作为依赖项。OpenZeppelin 是一个包含可复用的安全智能合约的开源库，并包含了一些经过验证的标准实现。我们将用它来获取 `ERC20` 合约的 ABI，以便创建 `web3` 合约实例。我们还会添加 `bignumber.js` 库，用于在整个应用中处理一些数值。

```
npm install web3@1.2.0 openzeppelin-solidity@2.1 bignumber.js@8.0
```

和之前一样，尝试运行 `npm start` 以确保这个示例 React 应用能够成功运行。现在我们可以开始编写代码了。

### 初始化 Web3

我们将像之前一样创建一个 `network.js` 文件来管理 `web3` 对象，以便连接到网络。我们将依赖注入式 Web3 提供器，并回退到 Infura 的 WebSocket 连接。请注意，由于我们不需要请求用户账户的访问权限，因此可以跳过 `ethereum.enable()` 调用。

```
// src/eth/network.js
import Web3 from 'web3';
let web3;
export function getWeb3() {
if (!web3) {
web3 = new Web3(window.ethereum
|| (window.web3 && window.web3.currentProvider)
|| "wss://mainnet.infura.io/ws/v3/PROJECT_ID");
} return web3;
}
```

### 注意

请确保为回退提供器填写有效的 URL，即使用你的 Infura 令牌，以防浏览网站的用户没有安装 Metamask 或兼容 web3 的浏览器。请注意，它是基于 WebSocket 的，因为我们稍后将在应用中使用事件订阅。

### ERC20 合约

我们将创建一个简单的函数来为 ERC20 合约初始化新的 web3 合约对象。回想一下，要实现这一点，我们需要访问 `web3` 对象（我们已经设置好了）、合约 ABI 以及合约地址。

```
// src/contracts/ERC20.js
import ERC20Artifact from 'openzeppelin-solidity/build/contracts/ERC20Detailed.json';
export default function ERC20(web3, address = null) {
const { abi } = ERC20Artifact;
return new web3.eth.Contract(abi, address);
}
```

我们从 OpenZeppelin 获取合约 ABI，同时暂时将地址留作参数。现在，我们已经完成了所有基本组件的设置，可以开始构建应用的视图部分了。

## 构建应用

我们将从一个主 `App` 组件开始，它将初始化连接并设置我们将要监控的 `ERC20` 合约实例。一旦合约实例就绪，我们将首先从中检索一些信息。

### 根组件

`App` 组件将是我们组件树的根节点（清单 4-11）。该组件将管理与网络的连接以及 `ERC20` 合约实例，这两者都将在 `componentDidMount` 生命周期方法中设置。

请记住，这个方法由 React 在组件挂载后自动触发，是用于检索异步数据的推荐事件。当然，如果你使用特定的状态管理库（例如 Redux），加载异步数据的策略可能会有所不同。

```
// src/App.js
import React, { Component } from 'react';
import ERC20Contract from './contracts/ERC20';
import { getWeb3 } from './eth/network';
const ERC20_ADDRESS = "0x1985365e9f78359a9B6AD760e32412f4a445E862";
class App extends Component {
state = { loading: true };
async componentDidMount() {
const web3 = getWeb3();
const erc20 = await ERC20Contract(web3, ERC20_ADDRESS);
this.setState({ erc20, loading: false });
}
render() {
return (

{ this.getAppContent() }

);
}
getAppContent() {
const { erc20 } = this.state;
if (!erc20) {
return (连接到网络...);
} else {
return (ERC20 合约地址为 {erc20.options.address});
}
}
}
清单 4-11
应用的根 App 组件的初始版本。请注意 componentDidMount 中高亮显示的那一行，它在设置 ERC20 合约时使用了我们之前构建的工厂方法
```

由于上述示例中的地址指向主网上的一个合约，因此该应用只有在连接到以太坊主网时才能工作。如果我们连接到其他网络，该合约很可能不存在于所列地址，从而导致应用失败。

### 注意

如果你对选择的 `ERC20` 地址感到好奇，它是 Augur 的 REP 代币。Augur 是一个去中心化的预言机和预测市场，其主要代币用于事件结果的报告和争议。如果你想尝试其他 ERC20 代币，Etherscan 在 `etherscan.io/tokens` 上提供了一个方便的热门代币列表。

为了处理这种情况，我们将添加几行代码来检测当前网络，并验证我们确实在主网上（清单 4-12）。你可以在应用运行期间通过更改 Metamask 上的当前网络来试验这个功能。

```
async componentDidMount() {
const web3 = getWeb3();
const networkId = await web3.eth.net.getId();
const isMainnet = (networkId === 1);
this.setState({ isMainnet });
if (isMainnet) {
const erc20 = await ERC20Contract(web3, ERC20_ADDRESS);
this.setState({ erc20 });
}
this.setState({ loading: false });
}
清单 4-12
检查我们当前是否连接到主网。请注意，合约只有在我们处于正确网络时才会被实例化
```

`render` 方法也需要相应地进行修改，以处理加载状态、已加载状态以及我们不在主网上的状态。

```
getAppContent() {
const { loading, isMainnet, erc20 } = this.state;
if (loading) {
return (连接到网络...);
} else if (!isMainnet) {
return (请连接到主网);
} else {
return (ERC20 合约地址为 {erc20.options.address});
}
}
```

我们还有一个问题需要解决：如果连接失败了呢？我们正在连接的节点可能离线，或者合约地址可能不正确。我们需要在检索区块链数据时添加适当的错误处理（清单 4-13）。

```
async componentDidMount() {
const web3 = getWeb3();
await this.checkNetwork(web3);
await this.retrieveContract(web3);
this.setState({ loading: false });
}
async checkNetwork(web3) {
try {
const networkId = await web3.eth.net.getId();
const isMainnet = (networkId === 1);
this.setState({ isMainnet });
} catch (error) {
console.error(error);
this.setState({ error: `连接网络时出错` })
}
}
async retrieveContract(web3) {
if (!this.state.isMainnet) return;
try {
const erc20 = await ERC20Contract(web3, ERC20_ADDRESS);
this.setState({ erc20 });
} catch (error) {
console.error(error);
this.setState({ error: `检索合约时出错` })
}
}
清单 4-13
更新后的 componentDidMount 方法，添加了错误处理。请注意，render 方法也需要相应更新，以在组件状态中存在错误信息时显示它
```

现在我们可以专注于代币本身了。让我们创建一个名为 `ERC20` 的新组件，用它来显示合约地址（清单 4-14），并开始开发这个组件。

```
getAppContent() {
const { loading, error, isMainnet, erc20 } = this.state;
if (error) {
return ({error});
} else if (loading) {
return (连接到网络...);
} else if (!isMainnet) {
return (请连接到主网);
} else {
return ();
}
}
清单 4-14
更新后的 render 辅助方法，用于显示 ERC20 组件，该组件将 erc20 合约实例作为属性传入
```



#### ERC20 组件

该组件负责接收合约实例，并知晓所有连接细节已由父组件处理完毕，随后展示该实例。我们将首先获取一些静态信息，例如名称、符号和小数位数（列表 4-15）。根据 ERC20 标准，这些属于可选属性，因为它们从未用作合约逻辑的一部分；尽管如此，大多数代币确实实现了它们。

我们还将获取代币的总供应量。这是为该合约创建的总代币数量。与名称、符号和小数位数不同，并非保证该值保持不变：某些代币采用**持续发行模式**，导致总供应量在每个区块上增加，而其他代币可能采用**通缩模式**，某些事件实际上会销毁代币。

就本应用而言，我们仅处理供应量固定的代币，因此无需刷新总供应量。不过，正如我们在本章前面看到的，你可以轻松添加一个轮询机制，以便在每次调用时更新总供应量。

```
class ERC20 extends Component {
async componentDidMount() {
const { contract } = this.props;
const [name, symbol, decimals, totalSupply] =
await Promise.all([
contract.methods.name().call(),
contract.methods.symbol().call(),
contract.methods.decimals().call(),
contract.methods.totalSupply().call(),
]);
this.setState({ name, symbol, decimals, totalSupply });
}
}
列表 4-15
用于展示 ERC20 代币信息的 React 组件。我们再次依赖 componentDidMount 方法来加载异步信息以填充状态。通过使用 Promise.all()，我们同时发起所有四个请求，并仅在获取全部返回值后设置它们。
```

现在，我们可以通过向用户展示代币名称和符号以及总供应量来呈现这些信息（列表 4-16）。我们将依赖代币的小数位数来格式化所有显示的值。

```
render() {
if (!this.state.totalSupply) return "加载中...";
const { name, totalSupply, decimals, symbol } = this.state;
const formattedSupply = formatValue(totalSupply, decimals);
return (
  <div>
    <h1>{name} 代币</h1>
    <p>总供应量 {formattedSupply} {symbol}</p>
  </div>
);
}
列表 4-16
用于展示 ERC20 代币静态信息的 render 方法。注意，totalSupply 已根据代币的小数位数进行了调整。
```

对于辅助函数 `formatValue`（列表 4-17），我们将依赖 `bignumber.js`，这是一个用于在 JavaScript 中操作和格式化大数的库。我们将总供应量值转换为 bignumber 实例，将其按小数位数右移（基数为 10），并将结果格式化为字符串。

```
// src/utils/format.js
import BigNumber from 'bignumber.js';
BigNumber.config({ DECIMAL_PLACES: 4 });
export function formatValue(value, decimals) {
const bn = new BigNumber(value);
return bn.shiftedBy(-decimals).toString(10);
}
列表 4-17
用于根据合约的 decimals 属性格式化代币金额的辅助函数。
```

假设你没有更改示例中的代币地址，那么输出目前应如图 4-3 所示。

![../images/476252_1_En_4_Chapter/476252_1_En_4_Fig3_HTML.jpg](img/476252_1_En_4_Fig3_HTML.jpg)

图 4-3

我们的示例应用展示了 Augur REP 代币的静态信息：名称 (Reputation)、符号 (REP)，以及使用代币小数位数 (18) 格式化后的总供应量 (1.1e25)。

## 展示转账事件

现在我们将添加应用的最后一个组件，该组件将展示代币的最新转账记录，并实时监听任何新转账事件。

### 加载历史转账

首先，向已有的 ERC20 组件中添加一个 `Transfers` 组件（列表 4-18），该组件将首先加载历史转账记录。此组件将从其父组件接收 `contract`、`decimals` 和 `symbol` 作为 props：第一个用于监听事件，另外两个用于格式化值。

```
render() {
if (!this.state.totalSupply) return "加载中...";
const { name, totalSupply, decimals, symbol } = this.state;
const { contract } = this.props;
const formattedSupply = formatValue(totalSupply, decimals);
return (
  <div>
    <h1>{name} 代币</h1>
    <p>总供应量 {formattedSupply} {symbol}</p>
    <Transfers contract={contract} decimals={decimals} symbol={symbol} />
  </div>
);
}
列表 4-18
更新后的 ERC20 组件的 render 方法，包含了新的 Transfers 组件。
```

我们将加载过去 1000 个区块内的所有转账事件，以初始化该组件。尝试加载全部历史转账记录可能会失败，因为在撰写本文时，REP 已有超过 75,000 笔转账，数量过大，无法在单个请求中从节点查询。还有其他代币拥有更多转账记录，例如 OMG，在撰写本文时已超过 200 万笔。

```
// src/components/Transfers.js
import React, { Component } from 'react';
import { getWeb3 } from '../eth/network';
export default class Transfers extends Component {
async componentDidMount() {
const { contract } = this.props;
const blockNumber = await getWeb3().eth.getBlockNumber();
const EVENT = 'Transfer';
const pastEvents = await contract.getPastEvents(EVENT, {
fromBlock: blockNumber - 1000,
toBlock: blockNumber
});
this.setState({
loading: false,
transfers: pastEvents.reverse()
});
}
}
```



### 注意

出于本应用的考虑，我们只是从任意数量的区块之前加载事件，以便在组件加载时为其注入初始数据。根据你的用例，你可能希望添加一个选项，通过触发后续的 `getPastEvents` 调用来加载更多事件（例如，当用户滚动到列表末尾时）。

该组件的渲染方法很直接：我们将展示一个纯组件，以列表形式显示每次转账（清单 4-19）。我们会传递代币符号和精度，用来格式化每次转账事件中转移的代币数量。

```
render() {
const { loading, transfers } = this.state;
const { decimals, symbol } = this.props;
if (loading) return "Loading...";
return (
{ transfers.map(transfer => (

)) }
)
}
清单 4-19
通过使用一个仅显示接收数据的纯组件来展示集合中的每次转账
```

请记住，React 要求我们为集合中的每个元素分配一个唯一的键。为了生成这个键，我们使用了一个 `getLogId` 辅助函数（清单 4-20），该函数结合了事件发生的交易哈希和日志索引（即该特定事件在交易发出的所有日志数组中的索引）。这种组合保证了唯一性。

```
function getLogId(log) {
return `${log.transactionHash}.${log.logIndex}`;
}
清单 4-20
用于为日志生成唯一标识符的简单函数。请注意，web3.js 已经为日志条目分配了一个 ID，该 ID 是通过对相同参数进行哈希计算得出的。然而，该哈希值会被截断为 4 个字节，如果处理大量事件，可能会产生冲突。
```

对于 `Transfer` 纯组件（清单 4-21），我们将仅仅依赖之前使用过的 `formatValue` 辅助函数，并从转账对象中获取 `from`、`to` 和 `value` 参数。作为额外功能，我们将在 etherscan 上包含一个交易链接，以便用户可以在那里查看交易，从而能够对我们应用上显示的数据进行额外检查。^(⁶⁸)

```
const ETHERSCAN_URL = 'https://etherscan.io/tx/';
export default function Transfer (props) {
const { decimals, symbol, transfer } = props;
const { from, to, value } = transfer.returnValues;
const roundedValue = formatValue(value, decimals);
const url = ETHERSCAN_URL + transfer.transactionHash;
return (

{from} 转账给 {to}，金额为 {roundedValue} {symbol}

);
}
清单 4-21
用于显示从 ERC20 代币合约加载的单个转账事件的 Transfer 组件
```

有了这段代码，每次我们重新加载页面时，都会看到该代币在最近 1000 个区块内的转账记录。现在我们可以添加对新发生的转账进行监听的支持。

### 监控新转账

为了监控新转账，我们将为该合约的 `Transfer` 事件安装一个*订阅*（清单 4-22），正如本章之前所介绍的那样。我们将从用于获取过去事件的区块之后的下一个区块开始，监听所有转账，并在组件卸载时取消订阅（清单 4-23）。

```
componentWillUnmount() {
const { eventSub } = this.state;
if (eventSub) eventSub.unsubscribe();
}
清单 4-23
当组件即将从树中卸载时，停止监听事件的代码。即使在这个特定应用中我们永远不会卸载该组件，但在不再使用订阅时始终移除它们是一个好习惯。
```

```
subscribe(contract, fromBlock) {
const eventSub = contract.events.Transfer({ fromBlock })
.on('data', (event) => {
this.setState(state => ({
...state,
transfers: [event, ...state.transfers]
}));
});
this.setState({ eventSub });
}
清单 4-22
订阅函数，用于监听新的转账事件，将在 componentDidMount 中调用，使用 erc20 和 blockNumber+1 作为参数。请注意，我们将订阅对象存储在状态中，以便稍后能够取消订阅。
```

现在，你的应用应该能在新交易发生时实时显示它们，而无需刷新页面。但是，为了确保我们正确处理所有场景，我们将为以下情况添加处理程序：由于区块链重组导致转账事件被移除，以及订阅失败的情况（清单 4-24）。

```
subscribe(contract, fromBlock) {
const eventSub = contract.events.Transfer({ fromBlock })
.on('data', (event) => {
this.setState(state => ({
...state,
transfers: [event, ...state.transfers]
}));
})
.on('changed', (event) => {
this.setState(state => ({
...state,
transfers: state.transfers.filter(t =>
t.transactionHash !== event.transactionHash
|| t.logIndex !== event.logIndex
)}))
})
.on('error', (error) => {
this.setState({ error })
});
this.setState({ eventSub });
}
清单 4-24
为订阅的 changed 和 error 事件添加处理程序。前者在事件从区块链中移除时触发，因此我们将其从状态中移除；后者在发生错误时触发，我们将错误添加到状态中以显示给用户。
```

### 等待确认

作为应用的最后一步，我们将避免向用户显示未确认的转账。我们不会在收到转账事件时立即显示它，而是等待一定数量的区块被添加到链中后，再将其渲染到我们的列表中。

为此，我们首先需要监控当前的区块号。我们将专门为此添加一个订阅（清单 4-25），尽管我们也可以每秒轮询 `getBlockNumber` 方法来达到类似的效果。

```
async componentDidMount() {
const blockNumber = await getWeb3().eth.getBlockNumber();
this.setState({ blockNumber });
const HEADERS = 'newBlockHeaders';
const blockSub = getWeb3().eth.subscribe(HEADERS)
.on('data', ({ number }) => {
if (number) this.setState({ blockNumber: number});
});
this.setState({ blockSub });
// 订阅新转账并加载之前的记录
// ...
}
清单 4-25
更新后的 componentDidMount 部分，在组件状态中设置初始区块号，并添加一个订阅，以便在接收到新区块时更新该区块号。
```

现在，我们可以限制组件只渲染至少发生在一定数量区块之前的转账事件（清单 4-26）。通过将交易发生的区块号与当前区块号进行比较，我们可以轻松实现这个过滤器。

```
render() {
const { transfers, blockNumber } = this.state;
const confirmed = transfers.filter((transfer) => (
blockNumber - transfer.blockNumber > 12
));
// 仅渲染已确认的转账
// ...
}
清单 4-26
更新后的 render 方法，仅显示至少有 12 次确认的转账。
```

### 注意

不同的应用对确认次数有不同的要求，有些应用甚至需要多达数百个区块。这将严格取决于你的具体用例。



## 总结

本章深入探讨了如何从以太坊网络中提取数据并将其输入到我们的应用程序中。我们首先回顾了与节点的连接方式，列出了`JSON-RPC`接口可用的协议，并研究了`web3`及其他库用于管理底层连接的`Provider`对象。我们还了解到可用的节点类型有所不同，以及像由支持`web3`的浏览器注入的`Provider`这样的对象，如何通过使用子提供程序（subproviders）来抽象掉部分差异。

我们以`web3.js`作为示例库，研究了可以向区块链发出的查询类型：通用网络信息、特定地址数据（如余额和代码），以及调用现有合约。当连接到归档节点时，这些查询可以针对过去的任何区块，而不仅仅是最近的区块。

我们还研究了在应用程序中实时监控合约变更的不同方法。轮询是一种始终可用的经典方法，但事件过滤器或订阅可能是更优的选择，因为它们性能更好或通知速度更快。

在章节末尾，我们构建了一个用于从`ERC20`代币合约中检索信息，并使用订阅监控其所有转账事件的应用程序。在下一章中，我们将学习如何对区块链进行更改，深入探讨向网络发送新交易的所有细节。

脚注 1 2 3 4 5 6 7 8 9 10

