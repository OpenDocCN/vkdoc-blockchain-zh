# 第 4 章：区块链商业应用

第二种分类将代币划分为同质化代币与非同质化代币（NFT）。同质化代币可以相互兑换，且面额相同的两个代币具有同等价值。美元就是一种同质化代币——我口袋里的十美元钞票可以与你口袋里的十美元钞票互换，因为两者价值相同。NFT 则是独一无二的资产；也就是说，每个 NFT 都是唯一的。一个 NFT 不能与另一个 NFT 互换。区块链上的 NFT 本质上就是一段无法更改的“数据块”，其所有权可以被验证。NFT 的所有权可以交换（通常需要支付对价），这笔支付则代表了赋予该 NFT 的价值。我们可以将 NFT 视为一种防止艺术品、纪念品或任何个人认定为艺术品的东西被伪造的机制.4。

我们以指出代币监管取决于其解释方式作为本次讨论的结尾。如果代币被视为证券，那么在美国，它属于证券交易委员会（SEC）的管辖范围。如果代币被视为商品，那么在美国，它属于商品期货交易委员会（CFTC）的管辖范围。同样，根据代币的用途和解释方式，多个其他监管机构也可能参与其中 5。代币监管是区块链系统设计者在设计应用时应关注的领域，并需牢记该监管尚处于萌芽且不断发展的阶段。



4. 请参阅使用以太坊创建 NFT 的分步指南：[www.wsj.com](https://www.wsj.com/articles/i-gave-my-mom-a-crypto-wallet-a-simple-guide-to-nfts-blockchain-and-more-11639404001?st=sweoi7bt6baes4o&reflink=desktopwebshare_permalink)  
   [com/articles/i-gave-my-mom-a-crypto-wallet-a-simple-guide-to-nfts-](https://www.wsj.com/articles/i-gave-my-mom-a-crypto-wallet-a-simple-guide-to-nfts-blockchain-and-more-11639404001?st=sweoi7bt6baes4o&reflink=desktopwebshare_permalink)  
   [blockchain-and-more-11639404001?st=sweoi7bt6baes4o&reflink=desktopweb](https://www.wsj.com/articles/i-gave-my-mom-a-crypto-wallet-a-simple-guide-to-nfts-blockchain-and-more-11639404001?st=sweoi7bt6baes4o&reflink=desktopwebshare_permalink)  
   [share_permalink](https://www.wsj.com/articles/i-gave-my-mom-a-crypto-wallet-a-simple-guide-to-nfts-blockchain-and-more-11639404001?st=sweoi7bt6baes4o&reflink=desktopwebshare_permalink)

5. [www.globallegalinsights.com/practice-areas/blockchain-laws-and-](http://www.globallegalinsights.com/practice-areas/blockchain-laws-and-regulations/usa)  
   [regulations/usa](http://www.globallegalinsights.com/practice-areas/blockchain-laws-and-regulations/usa) 和 [www.lw.com/thoughtLeadership/gli2021-blockchain-](http://www.lw.com/thoughtLeadership/gli2021-blockchain-crypto-not-in-kansas-anymore-)  
   [crypto-not-in-kansas-anymore-](http://www.lw.com/thoughtLeadership/gli2021-blockchain-crypto-not-in-kansas-anymore-) 是了解代币监管环境的两个优秀资源。

## 第 4 章 区块链商业应用

- **智能合约** – 到目前为止，我们看到交易代表了价值交换的语义，而代币代表了价值交换的单位。智能合约是交换条款的可执行软件表示。之前，当我们描述银行存款将导致利息存入账户时，正是与存款关联的智能合约决定了利率和利息支付的频率。区块链应用设计人员的设计问题是确定需要多少个智能合约、每个智能合约的逻辑，以及开发、验证和测试智能合约的流程。不同的区块链实现为其智能合约提供了不同的编程语言和不同的开发工具。在下一章中，我们将简要概述以太坊智能合约的开发工具。在本书的第二部分，我们将深入讨论使用以太坊的分布式应用开发。

智能合约存在两个复杂之处。这两个复杂之处都会影响智能合约中编码的业务逻辑的复杂度。第一个复杂之处与执行智能合约所需的计算能力有关。一般来说，更复杂的智能合约可能包含复杂的循环和多层嵌套的条件逻辑。由于每个区块链节点都会执行智能合约，因此节点为之消耗算力和电力的当事方需要提供补偿。逻辑复杂的智能合约可能会成本高昂。例如，如果用于计算存款利息的智能合约非常复杂，那么银行账户所有者和/或银行将不得不为智能合约的执行付费，从而降低其经济利益。

第二个复杂之处在于，一旦智能合约被提交到区块链，就无法更改。智能合约内的逻辑越复杂，我们的测试和验证过程未能发现所有软件缺陷的概率就越大。任何残留的软件缺陷都可能导致财务或安全漏洞。Mehar 等（2019 年）对一个旨在实现某功能的以太坊智能合约中的缺陷进行了批判性分析。



