# 第 11 章 构建团队项目

前面的例子展示了如何构建一个添加跨链 ID 的网页，以与已部署的智能合约进行交互。也可以为其他功能构建类似的页面，例如列出跨链 ID、修改 `chainId` 信息、批准或撤销跨链 ID 等。

## 安全审查

安全性需要考虑许多因素。应遵循[第 8 章](https://doi.org/10.1007/978-1-4842-8164-2_8)中的安全指南。智能合约中的每个功能都应有权限检查。例如，在修改跨链 ID 信息时，应检查请求者，确保其为跨链 ID 的原始注册者。第二个检查是跨链 ID 的状态应处于 `"Pending"` 状态。如果跨链 ID 处于 `"Verified"` 或 `"Revoked"` 状态，则其信息不能被修改。

另一个重要方面是确保只有管理员才能批准或撤销跨链 ID 的注册。该管理员可以是一个单一账户，也可以是联盟中的多签账户。管理员可以将其所有权转移给另一个账户。

除了智能合约的安全性，还应对网页的安全性进行评估。对于要部署到生产环境的服务，最佳实践是进行安全审计和白盒测试，以确保不存在重大安全漏洞。

## 部署至测试网

在编写智能合约和网页图形界面的代码时，开发者可以拥有一个开发系统和本地网络服务来测试项目。本地开发完成后，可以将其部署到公开的测试网供外部用户测试。以太坊测试网有多种选择。以太坊区块链有四个流行的可用测试网，包括 Ropsten、Kovan、Rinkeby 和 Goerli 测试网。这里提到的所有测试网都使用权威证明（`POA`）共识，并且比主网更快。要使用这些测试网，首先将 `MetaMask` 钱包连接到您希望部署智能合约的测试网（图 11-8）。测试网已添加到 `MetaMask` 中。如果您看不到列出的测试网，只需单击“显示/隐藏测试网”以打开配置小部件，并将“显示测试网”设置为“开”。

***图 11-8.** 通过 `MetaMask` 连接以太坊主网或测试网*

将 `MetaMask` 钱包设置为指定的测试网后，测试人员需要获取一些测试以太币来测试 dApp。这些测试网都通过专用的水龙头提供少量用于测试目的的以太币。水龙头地址和区块浏览器 URL 如下所示（图 11-9）：

***图 11-9.** 以太坊测试网水龙头地址*

一旦智能合约部署到测试网，并且网页设置为访问该智能合约，项目团队就可以宣布进行 alpha。

## 将去中心化应用部署到主网

在向用户发布或测试版程序时，测试计划需要社区成员参与试用去中心化应用并报告项目中发现的问题。通常情况下，会向报告问题的用户发放赏金。由于区块链的不可篡改性，去中心化应用在部署到主网之前，必须在测试网上进行彻底测试。

## 部署到主网

一旦去中心化应用在测试网上经过彻底测试，就可以部署到以太坊主网。将智能合约部署到生产网络的工具和方法与测试网相同。生产网络没有水龙头，部署智能合约所需的以太币需要购买。部署需要使用非托管账户，开发者实际持有私钥。`Coinbase` 和 `PayPal` 等公司托管的账户无法使用。开发者需要拥有账户的私钥才能部署智能合约。

此外，部署到主网时还需考虑以下几个因素。

首先是安全性。部署智能合约是通过向区块链的地址 `0` 发送交易来完成的。智能合约地址仅通过对发送者地址和该交易的 `nonce` 进行哈希运算生成。交易的 `data` 字段将是智能合约的字节码，并保存到区块链上计算出的地址上。一旦智能合约部署完成，该智能合约的所有者就是发送该交易的发送者。该发送者可能被授予修改区块链的权限。保护智能合约所有者地址非常重要。有时，智能合约所有者可以调用一个函数来放弃所有权或将其所有权转让给另一个外部拥有账户。

其次，智能合约地址需要在可信媒体上发布。用户通过向智能合约地址发送交易来与之交互。如果用户将交易发送到错误的智能合约地址，虚假的智能合约可能会截获发送给它的资金。

第三，与项目智能合约关联的代币需要从用户的钱包中添加。应向用户提供相关说明。对于大多数加密货币钱包，代币不会自动添加到钱包中。用户需要通过添加智能合约、代币符号、小数位数等信息来将代币添加到钱包。这些信息来自项目团队，并应发布给用户。

第四，应选择一个稳定的 `RPC` 节点并连接到 `MetaMask` 或其他钱包。`RPC` 节点是一个以太坊客户端节点，它将所有区块同步到系统并打开与 `Web3` 客户端的连接。`RPC` 节点可以由第三方或项目团队自己拥有。无论哪种情况，都应保护 `RPC` 节点免受攻击。

最后，网页和智能合约应正确集成。在网页中，`Web3` 对象构造交易并将请求发送到区块链。评估 `Web3` 和其他脚本以确保它们不被篡改非常重要。

一旦智能合约部署到主网，它就对全世界开放。除了项目团队提供的 Web 门户和 `CLI` 等接口外，第三方开发者也可以编写应用程序来访问该智能合约。由于区块链的“开放性”、“去中心化”和“不可篡改性”，去中心化应用的运行比常规应用更具挑战性，需要进一步讨论。

## 运营与升级考量

由于去中心化应用不应该有中心化的所有者和管理者，因此需要建立一个能够管理去中心化应用运营和升级的社区。许多项目通过构建投票机制来决定项目的运营和升级。项目团队发行治理代币并将其分发给社区成员。投票权与治理代币的数量成正比。社区成员可以通过赚取治理代币来提高其投票能力。当提出新功能或升级时，该提案会被发送到投票系统，每位社区成员可以投票支持或拒绝该提案。被接受的提案由开发者以开源方式实施，然后部署到主网。

## 构建去中心化应用的投票与升级机制

一个主要的挑战是，当发现安全漏洞时如何升级已部署的智能合约。由于没有中央权威机构，社区需要投票决定是否进行升级。然而，由于区块链是不可变的，已部署的智能合约无法被打补丁或升级。

在这种情况下，智能合约需要重新部署。在重新部署新智能合约时，由旧智能合约管理的代币需要迁移到新智能合约。当新智能合约部署后，所有网页和 `Web3` 接口都应指向新的智能合约地址。这是一个非常繁琐且容易出错的过程。

有时，编写可升级的智能合约是更好的选择。这需要将一个智能合约拆分为两个或多个智能合约。第一个智能合约是一个入口智能合约，功能非常简单，例如接收一个地址并调用该地址的目标智能合约。目标智能合约是实现了大部分功能的合约。如果目标智能合约存在安全漏洞并需要升级，可以部署一个新的目标智能合约，并将新地址发送给入口智能合约。当入口智能合约接收到新的目标智能合约地址时，它会调用新的目标智能合约。通过这种可升级的智能合约设计，网页和 `CLI` 无需重新配置，因为入口智能合约的地址保持不变。

为了最优地运营去中心化应用，构建一些服务来监控关键参数是至关重要的。例如，应监控总的代币铸造量，以确保黑客没有铸造额外的代币。还可以监控交易吞吐量和 `gas` 费用，以确保 dApp 处于健康的运行状态。此外，还应监控 `EVM` 的升级和以太坊的硬分叉，以确保去中心化应用不会受到负面影响。

最后，一个充满活力的社区是成功运营去中心化应用的关键。构建一个 dApp 的过程与构建一个社区密不可分。只有社区积极参与，去中心化应用才能持续、发展并抵御严重的攻击。

## 索引

**A**

*   `Atomic value exchanges`, 16
*   `Auditors`, 329
*   `Abstract class`, 295
*   `Authorization through tx.origin`, 282, 283
*   `Address type`, 258, 259
*   `Alternate currencies`, 37
*   `Automated Market Maker (AMM)` mechanism, 224, 386
*   `Application Bytecode Interface (ABI)`, 236, 246, 310
*   `Application design decisions`
    *   `consensus`, 132, 133
    *   `data`, 130–132
    *   `development stack`, 135–138
    *   `smart contracts`, 128, 129
    *   `stakeholder` organization, 133–135
    *   `tokens`, 124–127
    *   `transactions`, 122–124
*   `Application themes`
    * `data sovereignty`, 117
    * `payments`, 117
    * `transparency`, 117
*   `Arbitration`, 224
*   `Arithmetic operations`, 280, 281, 370, 400
*   `Assert() function`, 279
*   `Assert violation`, 279
*   `Asset management tools`, 221
*   `Asymmetric key cryptography`, 48, 49, 55

**B**

*   `Backward compatible`, 412
*   `Beacon chain`, 371
*   `Bitcoin`, 12, 13, 164, 209
    *   `block`, 167, 175
    *   `block header fields`, 176
    *   `blocks`, 177
    *   `coinbase transaction`, 172
    *   `components`, 167
    *   `consensus mechanism`, 184–187
    *   `design decision`, 181
    *   `distributed ledger software`, 169
    *   `economics`, 182–184
    *   `fields`, 174
    *   `hash Merkle root of` transactions, 177
    *   `inputs and outputs structure`, 170
    *   `miners`, 166
    *   `mining node`, 166
    *   `mining reward`, 197

© 张伟嘉和 Tej Anand 2022
W. Zhang and T. Anand, *区块链与以太坊智能合约解决方案开发*，

**B（续）**

**比特币**（*续*）

区块链应用，139  
自然分叉，180  
架构层，136  
网络软件开发流程，138  
组件，169  
成功因素，113、114  
节点，166、169

**基于区块链的解决方案**，120、157  
无许可公开型

**基于区块链的系统**  
区块链，197  
解决方案，119  
工作量证明共识机制，197

**区块链以太坊经典（`ETC`）**，405  
脚本，175  
智能合约，197  
交易数据结构，170  
交易生命周期，165  
交易，167、176  
名义交易中的数值，171  
钱包软件组件，168

**比特币改进提案（`BIP`）**，196

**区块链**，168、407、408  
社区，406  
共识，84  
分布式账本，84  
实现分类，95–99  
局限性，102–106  
工作量证明，213  
隐私性，87  
溯源，87  
纯粹主义视角，99  
智能合约（参见“智能合约”）  
特定变量，268–270  
技术集成，71–74

**区块头**，175

**鲍勃的 `ping` 函数**，280

**布尔型**，255、256

**借入加密货币**，386

**浏览器客户端**，307

**暴力搜索过程**，74

**可销毁性**，393

**业务问题**，116、119、121

**业务流程**，115

**企业对企业交易**，20

**业务用例**，117

**字节数组**，259、260

**字节码**，305

**32 字节跨链 `ID`**，410

**拜占庭容错（`BFT`）**  
区块链，61、72、102、214、343、346、350

**C**

**`CAP` 定理**，66、67、75

**`CAP` 定理相关设计权衡**，73

**碳信用市场**，389

**碳信用代币**，390

**中央银行数字货币（`CBDC`）**，143、209、379

**中心化账本**，88、89

**中心化所有权**，69

**`Chainlink`**，225

**`ChallengeWithdraw` 函数**，349

**`checkMerkleTree` 函数**，353、355、357

**`checkNotEqual()` 函数**，300

**`CLI` 客户端**，308

**客户端计算机**，68

**客户端节点**，212、372

**编码事件**，273

**`Coinbase` 交易**，172、219、223、230

**命令行界面（`CLI`）**，306、308、423

**社区型区块链网络**，135

**社区 `Telegram` 聊天群组**，325

**编译工具**，217

**区块链安全的复杂性**，319  
补丁与可升级性的约束，320  
区块链的去中心化特性，320  
对业务的高价值影响，321  
区块链的隐私与匿名特性，320  
无信任与无许可环境，320

**共识机制**，82、84、85、164、184–187

**`ConsenSys` 包**，382

**一致性**，72

**联盟型网络**，134

**约束条件**，140

**构造函数**，252

**合约账户**，190

**合约 `Bob`**，280

**核心区块链层**，213

**区块链的核心能力**，43

**跨链合约**，279

**跨链 `ID`**，407、412、413

**跨链标识符注册与查找服务**，408  
需求，409

**跨链身份服务**，413

**跨链 `ID` 注册**，431

**跨链 `ID` 智能合约**，418

**跨链互操作性工作组（`CITF`）**，408

**跨链操作**，412

**密码分析**，46

**加密货币**，227、390

**加密货币交易所**，223

**密码学密钥**，90

**密码学**，44、46、164

**`CryptoKitty`**，322、326、333

**密码学**  
非对称/公钥/私钥密码学，49  
密码分析，46  
密码学，46  
加密-解密流程，45  
高级加密-解密流程，45  
数据状态，55、56  
强非对称密钥算法，50  
强对称密钥算法，48

**D**

**每日交易量**，344

**`dApp` 客户端**  
浏览器客户端，307  
`CLI` 客户端，308  
`CLI` 格式，306  
桌面客户端，308  
`GUI` 格式，306  
移动客户端，307

**静态数据**，55

**传输中的数据**，55

**使用中的数据**，56

**数据相关低效问题**，25–27

**数据安全风险**，30、31

**数据共享**，5

**去中心化自治组织（`DAO`）**，129、142、209、226、379

**去中心化交易所（`DEX`）**，223、224、386

**去中心化保险平台**，226

**去中心化金融项目（`DeFi`）**，141、209、222、386、387

**委托权益证明**，185、186

**部署**，433

**`DepositEvent` 类**，274

**设计权衡**，104、121

**桌面客户端**，308

**开发区块链**，228

**开发栈**，135–138

**数字签名**，44  
万无一失的保证，51  
哈希，51、55  
局限性，51  
单向函数，52  
流程，50

**分布式应用（`dApps`）**，2、125、188、191、193、196、197、201

**分布式账本**，32、82、84、164

**分布式账本**（续）

- `block`, 85
- `Data storage location`, 284
- `databases and blockchain`, 94
- `Data structures`, 415
- `implementation`, 94
- `data type`, 255
- `peer nodes`, 91–94
- `Decentralization`, 4
- `privacy-preserving`, 90, 94
- `Decentralized applications`
- `rules`, 90
- `435`, `436`
- `transactions`, 93

## 索引

- `Distributed systems`, 44, 164
- `sources`, 23
- `CAP theorem`, 66
- `time to settle transactions`, 23
- `definition`, 56
- `Economics`, `Bitcoin`, 182–184
- `high availability`, `strong consistency`, and `network partition tolerance`
- `Emitted event`, 272–274
- `Encryption-decryption process`, 45
- `Energy consumption`, 102
- `issues`, `goals`, and `approaches`
- `Enterprise businesses`, 98
- `Enterprise Ethereum Alliance` (`EEA`), 139, 211, 215, 390, 408
- `Entertainment`
- `applications`, 155–157
- `Enum`, 264, 265
- `ERC20 specification`, 380
- `ERC20 tokens`, 380, 382, 383
- `ERC721 NFT token`, 361
- `ERP software`, 4
- `Drizzle`, 192
- `Dynamically size array`, 262, 263
- `Ethereum`, 99, 379, 380, 405

## E

- `Economic exchange`, 15
- `Economic inefficiencies`
- `Blockchain’s potential to address`, 31, 32
- `data-related inefficiencies`, 25–27
- `data security risks`, 30, 31
- `fees to third parties`, 24
- `fraud`, 29
- `potential of blockchain capability`, 33, 34
- `privacy trade-offs`, 29
- `regulations and rules constraints`, 28, 29
- `Bitcoin UTXOs`, 188
- `blockchain`, 196
- `boot nodes`, 212
- `client nodes`, 212
- `contract accounts`, 190
- `dApp architecture and development tools`, 192
- `distributed applications creation`, 191
- `Ether denominations`, 190
- `externally owned accounts`, 190
- `permissionless public blockchain`, 212
- `risks`, 189
- `SC`, 343–347

## Ethereum (续)

- `Ethereum blockchain platform`, 210
- `scalability and security`, 199
- `Ethereum blockchains`, 334, 406
- `Solidity`, 188
- `Ethereum-compatible blockchains`, 214
- `transactions`, 189
- `transactions types`, 190
- `Ethereum ecosystem projects`, 387
- `DAO`, 226
- `decentralized exchange` (`DEX`), 223, 224
- `Decentralized Insurance Platform`, 226
- `Fortmatic`, 221
- `hosted service`, 219
- `KYC and Identity`, 227
- `MetaMask`, 219, 220
- `MyEtherWallet` (`MEW`), 220, 221
- `NFT application`, 224, 225
- `Oracle service`, 225, 226
- `smart contract–enabled banking dApp`, 222, 223
- `Stablecoin`, 227
- `Ethereum EVM storage data`, 284
- `Ethereum gas fee`, 215
- `Ethereum gas mechanism`, 398
- `Ethereum improvement proposal` (`EIP`), 196, 408
- `Ethereum.js retrieves`, 428
- `Ethereum mainnet` (`ETH`), 341, 342, 347, 364, 405
- `EIP-155`, 406, 407
- `and ETC`, 406
- `and Ethereum Classic`, 405
- `plasma smart contract`, 348, 349
- `transactions`, 398
- `Ethereum network`, 212

## Ethereum (续)

- `Ethereum 1.5`, 370
- `Ethereum 2`, 213, 338
- `architecture`, 371–374
- `beacon chain`, 371
- `migration from Ethereum 1`, 374–376
- `mining node`, 376
- `POW to POS`, 371
- `running a validator nodes`, 376, 377
- `sharding`, 371
- `uncertainties`, 378
- `Ethereum blockchain`, 209, 212, 228, 246, 269, 335, 395, 396, 398, 403, 405, 406
- `advantages`, 209
- `and infrastructure`, 209
- `security` (参考`Security`)
- `smart contracts`, 319
- `Ethereum blockchain architecture`
- `application layer`, 217, 218
- `components architecture and overview`, 211
- `core blockchain layer`, 213–215
- `Enterprise components layer`, 215, 216
- `layers`, 211, 218
- `network layer`, 212, 213
- `tooling layer`, 216, 217
- `Ethereum public blockchain`, 215, 216, 231
- `Ethereum smart contract development`, 210
- `Ethereum testnet`, 431
- `Ethereum tools for smart contract development`
- `Etherscan`, 231
- `Geth`, 231–234
- `MetaMask`, 229–231
- `Remix`, 238–243
- `tools and components`, 228
- `Truffle`, 235–238
- `Ethereum virtual machine` (`EVM`), 191, 209, 214, 215, 241, 242, 245–247, 335, 371
- `Etherscan`, 231
- `Ether withdraw operation not protected`, 276
- `Events`
- `defining`, 271, 272
- `emitted`, 272–274
- `inheritable member`, 271
- `storing location`, 271
- `Exchange of money`, 7
- `Functional security holes`, 321
- `Functional security holes`, `smart`

## F

- `Financial services industry`
- `blockchain solutions`, 143
- `capabilities`, 140
- `CBDC`, 143
- `crypto exchanges`, 143
- `DeFi`, 141
- `economic inefficiencies`, 140
- `quintessential third party`, 140
- `redundant work/rework/reconciliation work`, 141
- `themes`, 141
- `Fixed size array`, 261, 262
- `Fortmatic`, 221
- `Founder-directed network`, 134
- `Frequent flier points`, 37
- `Function`
- `crosschain ID`, 416
- `getChainIdFromLegacyId`, 416
- `getChainIdInfo`, 416
- `getChainIdStatus`, 416
- `revoked`, 416
- `smart contract`, 416
- `Function access scopes`, 254

## 索引

## A

汇率，8

## C

`contracts`，交易所交易，9, 16

## D

`disabled smart contract`，323

## E

`Externally owned accounts`，190

## F

`fund deadlock`，321
`fund leakage`，322

`F`
`Orphan smart contract`，323
`Function call value is not` checked，276
`Fault tolerance`，59, 72
`Fault-tolerant distributed system`，60
`Filecoin project`，394
`Function modifier`，252
`Function-specific variables`，281
`Function visibility error`，274, 275
`Fungible tokens`，127

服务提供商资质认证，150
服务成本，145
利益相关者，147

第三方管理员，145–147

## G

`Ganache tool`，228
`Gas cost`，402
`Gas fee consideration`
生态系统，396
生态系统，195
Gas 消耗，395
量化计算，403
`Gas station`，397
`Geth`，231–234
`Geth client`，372
`getMessage function`，317
`Gift cards`，38
`GitHub location`，352
`GitHub storage`，241

## H

`Halting program operations`，401
`Hashed digital signatures`，54
`Hashing algorithms`，51–53
`hashMerkleRoot field`，177

医疗应用
倡导组织，147
理赔处理，150
临床试验，152
消费者与临床服务提供者的互动，145
经济效率低下，148, 149
医疗供应链，151

`Hyperledger`，97
区块链实现，193
生态系统，195
框架和工具，193, 195
治理模型，193, 196
`Hyperledger Fabric`，97

## I

`Indirect costs`，31
`Information exchange`，5
`Information technology (IT)`，3, 30, 147
`Institute for Applied Network Security (IANS)`，30
`Integer`，256–258
`Integrated development environment (IDE)`，240, 247
`Interfaces`，296
`Internal functions`，254
`Internet`，4
`InterWork Alliance (IWA)`，125, 390

## J

`JavaScript console`，232, 234
`JavaScript files`，427
`JavaScript scripts`，307
`JavaScript VM`，242
`JSON RPC`，217

## K

`Know Your Customers (KYC)`，227

## L

`Layer 2`
机制，343
性能和可扩展性，343
`Plasma`（参见`Plasma`）
`Rollup`，363–370
`SC`，343–347
智能合约，343
`leaf nodes`，351
`Library`，296
`Logical operators`，255

## M

`Mallory contract`，280
`Mapping data structures`，415
`Mapping data type`，263, 264
`Membership rewards`，37
`Memory operations`，402

Merkle 树
二叉树结构，351
`checkMerkleTree function`，353, 355, 357
代码片段，352
数据完整性与处理效率，351
示例，351–357
`leaf nodes`，351
`MerkleDemo smart contract`，353
Plasma Cash，360–363
Plasma MVP，357–359
源代码，352
`testcasedemo function`，355, 357

`MetaMask wallet`，193, 216, 217, 219, 220, 229–231, 233, 242, 243, 307, 316, 346, 425, 433
`Migration from Ethereum 1 to Ethereum 2`，374–376
`Miner credentials`，374
`Miner node`，178
`Miner software component interfaces`，169
`Mintability`，392
`Mint event`，272
`Mobile client`，307

货币
比特币，13
挑战/缺点，10
商品，11
契约，7
定义，12
交换交易，8, 9
交换媒介，12
属性，11, 12
交易，7, 8
普遍接受的交换媒介，9
`msg variable`，269
`MyEtherWallet (MEW)`，220, 221
`MythX security verification`，289

## N

`NFT applications`，224, 225
`NFT marketplace`，386
`NodeKey`，212
`Nonfungible tokens (NFTs)`，127, 319, 360, 383, 392
`Nonnative tokens`，126

## O

`One-way functions`，52
`OpenSea`，225
`Open Systems Interconnection (OSI)`，211
`OpenZeppelin package`，382
`Optimistic layer 2`，364, 365
`Optimistic Virtual Machine (OVM)`，364
`Oracle service`，225, 226
`Ouroboros`，199
`Outdated compilers`，285, 286
`Out-of-bound index`，284
`Out-of-bound write`，284

## P

`P2P network layer`  
通信，337  
`Payable functions`，253  
`Peer-to-peer networks`，32, 44, 67–71  
`Permissioned blockchains`，96  
`Permissionless blockchains`，97

`Plasma`  
运营商，349, 350  
`Plasma` 链，350  
智能合约，348, 349  
交易 Merkle 树（参见 `Merkle 树`）  
交易/智能合约，350  
`Plasma blockchain`，349  
`Plasma Cash`，357, 360–363  
`Plasma chains`，350  
`Plasma MVP`，357–359, 362  
`Polkadot`，199  
`Privacy`，82, 107  
`Privacy preservation`，164  
`Privacy-preserving distributed ledgers`，90, 94  
`Privacy trade-offs`，29  
`Private keys`，73  
`Private permissioned blockchain`，95, 97  
`Private permissionless`，95  
`Proof of authority (POA)`，186, 213, 343, 346, 350, 432  
`Proof-of-stake (POS)`，185, 350, 371, 372, 374–377  
`Proof-of-work (POW)`，178, 338, 371, 377  
`Public blockchains`，96, 342  
`Public key`，48  
`Public permissioned blockchain`，95, 121  
`Public permissionless blockchain`，95, 97, 99

## 索引

## Q

二次算术程序，第 368 页

定量分析，第 398 页

量子计算，第 336–338 页

法定人数，第 142 页

## R

竞态条件，第 278 页

随机数生成器，第 286、331 页

秩-1 约束系统格式，第 368 页

`RCP` 协议，第 373 页

归约函数，第 52 页

关系型数据库管理系统，第 4 页

`Remix`，第 238–243、247、292 页

`Remix` 调试器，第 302 页

`Remix` 开发工具，第 309 页

`Remix` 插件，第 301 页

远程过程调用，第 193 页

`RETURN` 操作码，第 401 页

瑞波实验室，第 142 页

`Rollup`，第 363 页

- 乐观二层，第 364、365 页
- zk-SNARK，第 365 页

根区块链，第 348 页

`RPC` 端点，第 313 页

## S

`S&H` 绿色印章，第 37 页

`SC` 钱包，第 345 页

美国证券交易委员会，第 127 页

安全性

- 智能合约中可被攻击的安全漏洞，第 323–325 页
- 智能合约最佳实践（参见 安全实践，智能合约）
- 编译器漏洞，第 285、286 页
- 数据类型与数据漏洞
    - 任意存储位置，第 284、285 页
    - 通过`tx.origin`授权，第 282、283 页
    - 区块值，第 283 页
    - 状态变量遮蔽，第 281、282 页
    - 未使用变量，第 285 页
    - 变量值溢出/下溢，第 280、281 页
- 函数漏洞
    - 断言违例，第 279 页
    - 跨链合约，第 279 页
    - `delegatecall`函数，第 277 页
    - 因函数调用失败导致的拒绝服务攻击，第 278 页
    - 未受保护的以太币提取操作，第 276 页
- 函数调用值未经检查，第 276 页
- 竞态条件与交易顺序依赖，第 278、279 页
- 自毁函数，第 276 页
- Solidity 已弃用函数，第 277 页
- 可见性错误，第 274、275 页
- 签名漏洞，第 286、287 页

安全审计，第 329 页

安全实践，智能合约

- 区块链特定安全提示，第 334–336 页
- 缓解方案，第 326、327 页
- 量子计算，安全影响，第 336–338 页
- 可读的智能合约逻辑，第 330 页
- 审查 `Gas` 消耗，第 332、333 页
- 随机数生成器，第 331 页
- 安全漏洞与补丁，第 333、334 页
- 设置资产价值上限，第 327、328 页
- 智能合约开源，第 328 页
- 智能合约审计，第 329、330 页
- 源代码与库，第 327 页
- 经过充分测试的库，第 331 页
- 白帽黑客，第 329 页

安全漏洞，第 334 页

自限函数，第 253 页

自毁函数，第 276 页

`setMessage`函数，第 317 页

`sha256`/`keccak256`，第 351、362 页

`sha256` Solidity 函数，第 353 页

分片节点，第 371、373 页

签名操纵，第 286、287 页

小型智能合约，第 227 页

智能合约，第 382、414、415、423、431、434 页

- 区块链，第 247 页
- 面向业务人员，第 246 页
- 字节码，第 246 页
- 代码与解释，第 418 页
- 编译，第 246 页
- 组件，第 424 页
- 跨链 ID，第 416 页
- 定义，第 245 页
- 以太坊，第 246 页
- 函数调用，第 246 页
- Solidity（参见 Solidity 编程语言）
- 面向技术人员，第 246 页
- 技术视角，第 246 页
- 与 Web GUI，第 431 页

智能合约字节码，第 236 页

智能合约开发工具

- `Mythx`，第 288、289 页
- Solidity 调试
    - 调试智能合约，第 303–305 页
    - 启用调试器，第 302 页
    - 启动调试器，第 302 页
    - 源代码，第 301 页
- Solidity 测试，`Remix` 插件，第 296–298、300、301 页
- Solidity 转 UML
    - `dApp` 项目，第 292 页
    - `Remix`，第 292、293 页
    - 独立工具，第 293–296 页
- SSA，第 290、292 页

支持智能合约的银行 `dApp`，第 222、223 页

智能合约语言，第 218 页

智能合约，第 82、99–101、105、164、435 页

- 可被攻击的安全漏洞，第 323–325 页
- 审计，第 329、330 页
- 安全实践（参见 安全实践，智能合约）
- 与编写代码，第 325 页

智能钱包，第 343 页

软件应用，第 217 页

软件即服务，第 5 页

`sol2uml`包，第 293 页

`Solano`，第 199 页

`Solidity`，第 330 页

Solidity 数据类型

- 地址类型，第 258、259 页
- 区块链特定变量，第 268–270 页
- 布尔型，第 255、256 页
- 字节数组，第 259、260 页
- 代码安全与执行效率，第 255 页
- 动态大小数组，第 262、263 页
- 枚举，第 264、265 页
- 固定大小数组，第 261、262 页
- 整数，第 256–258 页
- 映射类型，第 263、264 页
- 智能合约函数，第 255 页
- 结构体，第 266–268 页
- 支持与不支持的类型，第 255 页

Solidity 已弃用函数，第 277 页

Solidity 编程语言

- 字节码，第 247 页
- 注释，第 249 页
- 构造函数，第 252 页
- 以太坊智能合约，第 247 页
- 事件，第 271–274 页
- 函数访问范围，第 254 页
- 函数修饰符，第 252、253 页
- Hello World 示例，第 248 页
- 导入文件，第 251、252 页
- 多重作用域，第 253、254 页
- 安全性（参见 安全性）
- 源代码，第 247 页

Solidity 智能合约编程，第 319、322 页

Solidity 静态分析，第 290、292 页

Solidity 单元测试插件，第 296–301 页

稀疏默克尔树，第 362、376 页

特殊全局变量，第 283 页

稳定币，第 227、387 页

稳定 `RPC` 节点，第 434 页

基于栈的编程语言，第 201 页

利益相关者组织模型，第 133 页

`StartWithdraw`函数，第 349 页

状态通道（L2），第 343–347 页

状态通道，第 363 页

状态通道（L2）

- 组件，第 344、345 页
- 链下计算，第 346 页
- 参与者，第 347 页
- 拓扑与工作流，第 344 页
- 交易，第 347 页
- 钱包，第 345 页
- 工作流，第 345 页

状态变量，第 281 页

存储，第 214 页

结构体，第 266–268 页

`SubmitPlasmaTxRecord`函数，第 348 页

供应链，第 388 页

面向供应链的交易，第 24 页

供应链应用，第 153、154 页

对称密钥密码学，第 47、55 页

系统设计者，第 116 页

## T

目标区块链医疗应用，第 150、158 页

`testcasedemo`函数，第 355、357 页

测试程序，第 433 页

第三方应用，第 234 页

第三方物流，第 19 页

第三方 `RPC` 节点，第 308 页

代币，第 434 页

代币，第 124–127 页

代币分类框架，第 125、391 页

传统计算机网络架构，第 68 页

交易，第 7、82、106、278 页

基于交易的分析，第 119 页

交易费，第 341 页

交易日志，第 271、274 页

旅行支票，第 38 页

`Truffle`，第 235–238 页

公理，第 6 页

信任，公司，第 6 页

`TTCDiploma` 智能合约，第 385 页

## U

`UML` 图，第 294 页

`Uniswap`，第 386 页

统一资源标识符，第 384 页

未花费交易输出格式，第 188、358–360 页

## V

验证节点，第 372、374 页

价值交换

- 数据角色，第 18、20 页
- 经济，第 15、22 页
- 参与方，第 17 页
- 专有数据，第 22 页
- 第三方角色，第 19 页
- 交易，第 88 页

价值类型，第 17 页

视图函数，第 253 页

## W

钱包软件组件，第 168 页

`Web` 客户端

- 编译智能合约，第 310 页
- 部署智能合约，第 311、313 页
- 以太坊开发区块链，第 308、309 页
- 编写，第 313–315、317 页

## X，Y

`0xcert`，第 225 页

## Z

零知识简洁非交互式知识论证

- 特性，第 366 页
- 定义，第 368 页
- 饮酒年龄，第 368 页
- 二层 `Rollup` 解决方案，第 367 页
- 在隐私计算中，第 367 页
- 隐私增强的加密计算，第 366 页
- 证明与验证，第 369 页
- `R1CS` 格式，第 368 页
- 价值，第 368 页
- 工作原理，第 369、370 页

*   目录
*   关于作者
*   关于技术审校
*   致谢
*   第 1 章：区块链的商业与经济动因
    *   引言
    *   货币简史
    *   作为价值交换的经济
    *   当前经济的低效问题
    *   区块链在解决当前经济低效问题上的潜力
    *   本章小结/要点
    *   边栏
    *   测验问题
    *   参考文献
*   第 2 章：支撑区块链的核心技术概述
    *   引言
    *   密码学与数字签名
    *   分布式系统
    *   点对点网络
    *   区块链技术集成
    *   本章小结/要点
    *   测验问题
    *   边栏：关键分布式系统术语与定义
    *   参考文献
*   第 3 章：区块链组件与架构
    *   引言
    *   区块链组件的概念性概述
    *   分布式账本与区块链技术概述
    *   区块链实现分类
    *   智能合约与区块链组件总结
    *   区块链局限性
    *   本章小结/要点
    *   边栏：区块链术语
    *   测验问题
    *   参考文献
*   第 4 章：区块链商业应用
    *   引言
    *   区块链是否必要？
    *   区块链应用设计决策
    *   区块链应用
    *   区块链金融应用
    *   区块链医疗应用
    *   区块链供应链应用
    *   区块链娱乐应用
    *   本章小结/要点
    *   测验问题
    *   参考文献
*   第 5 章：区块链实现概述：比特币、以太坊与超级账本
    *   引言
    *   比特币交易、区块与挖矿
    *   比特币经济学
    *   共识协议
    *   以太坊
    *   超级账本
    *   比特币、以太坊与超级账本的对比
    *   新兴区块链实现
    *   本章小结/要点
    *   边栏：基于栈的编程语言
    *   测验问题
    *   参考文献
*   无标题
*   第 6 章：以太坊架构与概述
    *   引言
    *   以太坊架构
        *   网络层
        *   核心区块链层
        *   企业组件层
        *   工具层
        *   应用层
    *   以太坊区块链生态系统与 `DeFi` 项目
        *   用于管理资产的钱包
            *   托管服务
            *   `MetaMask`
            *   `MyEtherWallet`
            *   `Fortmatic`
        *   支持智能合约的银行 `dApp`
        *   以太坊中的去中心化交易所
        *   `NFT` 应用
        *   预言机服务
        *   `DAO` 平台
        *   去中心化保险平台
        *   去中心化 `KYC` 与身份认证
        *   稳定币
    *   搭建智能合约开发环境的工具
        *   `MetaMask`：与以太坊交互的最简单方式
        *   `Etherscan`：最全面的区块链浏览器
        *   `Geth`：以太坊区块链的瑞士军刀
        *   `Truffle`：最全面的智能合约开发工具
        *   `Remix`：最便捷的基于 `Web` 的智能合约开发工具
    *   总结
*   第 7 章：使用 `Solidity` 编写智能合约
    *   引言：上一章学到的内容
    *   什么是智能合约
    *   什么是 `Solidity` 编程语言
    *   模块 1：`Hello World` Solidity 示例
        *   Solidity 注释
        *   Solidity 程序与版本声明
        *   导入 Solidity 文件
        *   构造函数
        *   函数修饰符
        *   区块链访问范围：`Pure`/`View`/`Payable` 函数
        *   函数访问范围：`Public`、`External`、`Internal` 和 `Private`
    *   模块 2：Solidity 数据类型
        *   布尔型
        *   整数类型
        *   地址类型
        *   字节数组
        *   固定大小数组
        *   动态大小数组
        *   映射数据类型
        *   枚举数据类型
        *   结构体数据类型
            *   区块链特定变量
    *   模块 3：事件
        *   什么是以太坊事件
        *   事件存储在哪里
        *   如何定义事件
            *   如何触发事件