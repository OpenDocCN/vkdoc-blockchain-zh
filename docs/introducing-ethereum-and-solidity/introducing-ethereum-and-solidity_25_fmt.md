# 补充货币

一个国家为何需要不止一种货币形式？在美联储（美国如今的中央银行）成立前的几十年里，有许多本地货币在流通。这些纸币通常代表着某处存储的黄金，因此本质上是本地化的；如果兑现机构远在千里之外，一张黄金凭证便价值甚微。在广泛、系统化的私人货币体系出现之前（美国历史上被称为“野猫银行”时代的一段时期），许多印刷厂的主要收入来源是印刷带有各种防伪特征的货币，以与竞争对手的印刷厂一较高下。

本杰明·富兰克林就是其中一位通过印刷补充货币而致富的印刷商。事实上，他以采取超乎寻常的防伪措施而闻名。据史密斯学会记载，他曾在印刷宾夕法尼亚州官方发行的本地货币时故意拼错州名，以期挫败那些以为这些钞票必定是假币的伪造者。¹ 富兰克林设计的许多殖民地纸币上都印有“伪造者处死”的字样。²

“补充货币”一词指的是与国家法定货币并行运作的交换媒介，用以满足国家货币无法满足的需求。这类货币通常有四个目的³：

- 促进小社区内的本地经济发展
- 在该社区中构建社会资本
- 培育更可持续的生活方式
- 满足主流货币无法满足的需求

使用`Solidity`编程，任何人都可以通过一个简单的代币合约创建补充货币。这些代币可以根据实际情况设置任何参数，正如你将在第 5 章部署代币合约时所看到的那样。

## Solidity 的前景

`Solidity`是一种高级的面向合约语言，与 JavaScript 和 C 语言有相似之处。它允许你开发合约并将其编译为`EVM`字节码。它目前是以太坊的旗舰语言。尽管它是为`EVM`编写的最流行的语言库，但它既不是第一种，也可能不会是最后一种。

以太坊协议中有四种处于相同抽象级别的语言，但社区已逐渐聚焦于`Solidity`，它已经超越了`Serpent`（类似于 Python）、类 Lisp 语言（`LLL`）以及已被弃用的`Mutan`。

学习`Solidity`能让你在任何基于以太坊的系统中转移有价值的代币。并且，由于以太坊和`Solidity`本身是免费且开源的，聪明的人很可能会对其进行修改并重新发布，或私下部署。事实上，已经有好几个团体这样做了；你将在后面的第 11 章中了解这些第三方及其方法。

你可以在`http://solidity.readthedocs.io/en/develop/index.html`找到官方的`Solidity`文档。不过，其他网站也提供了有用的`Solidity`文档。为了方便起见，所有最受欢迎的`Solidity`文档都链接在`http://solidity.eth.guide`子域名下。

## 浏览器编译器

测试`Solidity`最常见的方式是使用基于浏览器的编译器。你可以在`http://ethereum.github.io/browser-solidity`找到它。为快速参考，你也可以在`http://compiler.eth.guide`找到它。

如果你已经读到这里，或许已经对如何自学`Solidity`充满好奇。虽然如果你已经掌握了另一种编程语言，开始使用`Solidity`编程肯定会更容易，但如果你是非编程人员，也不必因此气馁。

## 学习为 EVM 编程

有时候，养成一个新习惯比改掉一个旧习惯更容易。分布式应用编程中的许多惯例，在今天网页和原生应用程序员看来可能显得古怪或奇特。而且，他们可能已经在其他语言或专业领域有了职业或个人的投入。所以，如果你刚刚起步，不必觉得全世界都领先于你。在以太坊的世界里，一切都还处于早期阶段。

> **注：** 关键编程术语会在学习过程中逐一解释，你也能从上下文中领悟很多。如果想对本章一些核心概念有更深入的理解，可以尝试阅读一本面向初学者的 JavaScript 书籍（`http://www.apress.com/us/book/9781484217863`）。

新程序员可以不带任何预设观念来接触以太坊。更棒的是，他们会发现一个（诚然，在花费一些时间之后）可以自上而下完全理解的系统。并非所有黑客，甚至软件工程师，都了解其应用托管提供商底层网络中错综复杂的细节。

在传统网页应用中，你有很多带有数据库的独立服务器，通过网络通信和共享数据。这些数据可能会被位于其他服务器上的应用程序所操作。甚至可能有更多服务器参与其中以平衡需求激增。

> **注：** 服务器是一种扮演特定角色的计算机，作为你希望通过网络提供给人们某种服务的一部分。有些服务器在称为数据库的系统中保存数据（可以想象成包含客户姓名和地址等信息表格）。有些服务器运行着其他计算机可以通过网络访问的应用程序。

在以太坊中，网络就是数据库，这个网络可以运行其上所有人都能访问的应用程序。因此，你最终会对这三方面都有相当深入的了解。

当你知道底层的运作原理时，观察区块链浏览器报告新的交易是一件令人惊叹的事情。尽管学习以太坊看似工作量很大，但要达到同样的广度和深度去理解当今的网络，则需要付出多得多的努力。

以下小节将介绍你应该开始尝试`Solidity`的其他理由。

### Easy Deployment

In Ethereum, you don't typically face the many troubles associated with deploying and scaling regular web applications. All the smart contracts needed for the backend of a distributed application (i.e., dapp) can be neatly packaged into a few documents and sent to the `EVM`, and then—your program can be immediately used by anyone on Earth with an Ethereum wallet or command-line node. Today, developers may want to build "hybrid" Ethereum dapps accessible through a regular web browser. In this case, adding Ether payments only increases the workload. However, as the network completes its construction over the next 2-3 years, it will become much easier to use the Ethereum protocol to host all components of an application.

#### Note

In business terms, time to value (abbreviated as TTV) is the time elapsed from when a customer requests something to when the customer receives it. This "something" can be tangible or intangible. A low `TTV` means it is easy to conceive a product or service and quickly deliver it to users who need it.

In Ethereum, developing and deploying applications that are immutable, always online, uncensorable, and can transfer real value across any distance is fast and inexpensive (though not necessarily easy). Apart from the gas fees incurred by your program and your own time (and computer), everything is free. For software engineers, service providers, system administrators, and product managers, the long-term impact of working in the Ethereum ecosystem means: more fragile systems, faster product iterations, and significantly reduced infrastructure development time needed to support new applications or services. In short, this could mean a substantial reduction in `TTV` for both enterprise software vendors and internal teams.

### Reasons to Write Business Logic in Solidity

Due to its novel features, the fate of Ethereum in 2017 and beyond does not necessarily depend on the mainstream popularity or adoption of today's Ethereum clients. Instead, it relies on the adoption by developers, brands, companies, organizations, governments, and other institutions that can create Ethereum tokens (and perhaps even their own branded wallets) for their communities.

They might do this to quickly and securely launch cool new products and services with ultra-low overhead. This also applies to large marketing campaigns—which now need to be deployed increasingly faster to keep pace with the spread of internet meme culture. The frictionless nature of payments in crypto networks makes it easier than ever to build seamless sales and marketing experiences, with payment functionality built-in.

A complementary currency is also a highly valuable tool for rewards programs, membership clubs, and large retail zones. Customers holding currency in the form of a brand token tend to spend more frequently on that brand, similar to how frequent flyers today remain loyal to the airline mileage and credit card rewards programs that give them the most value.

Today, loyalty programs can be obscure or even slightly fraudulent. But the transparency of blockchain-based loyalty tokens will make them as reliable as any other form of cryptocurrency—meaning they can be traded on exchanges or accepted as payment by other parties.

### Code, Deploy, Relax

Many applications supporting Ethereum are likely to be used through the `Mist` wallet or other Ethereum-native applications that run a node in the background. For client application developers, adding support for new Ethereum-based tokens is trivial, implying a high degree of overlap and interoperability between Ethereum wallets and tokens, much like the many email clients compatible with `IMAP` and `POP` today.

With some extra effort, it is also possible today to create an Ethereum program accessible on the regular web. However, deployment will become increasingly easier with the use of new third-party frameworks, examples of which are provided in Chapter 8.

This is not to say that traditional web applications will disappear. Many individuals and organizations have invested significant resources in them. Nevertheless, the Ethereum network makes deploying and operating applications at scale easier and cheaper, and as you will see, this will entice more and more teams to consider decentralizing their applications.

## Design Principles

The `Solidity` programming language has a syntax similar to `JavaScript`, but it is specifically designed to be compiled into bytecode for the Ethereum Virtual Machine. As mentioned in Chapter 3, the `EVM` runs fully deterministic code; the same algorithm with the same input will always produce the same result. You can prove this mathematically, which will be shown later in this chapter.

`Solidity` is statically typed, supporting inheritance, libraries, complex user-defined types, and other features. Using types judiciously helps programmers understand how their programs will execute. A list of types in `Solidity` is provided at the end of this chapter.

**Note:** `Data types` are exactly what they sound like. Programmers can choose to tell the machine what type of data to expect: for example, will it be a number or a string of letters? Loosely typed languages do not require the programmer to specify the exact type; strongly typed languages do.

Interestingly, in `Solidity`, you can write `assembly` code inline. If you wish to perform an operation using one of the `EVM` opcodes listed in Chapter 3, you can do so inline within your `Solidity` contract. Simply write `assembly {...}` and place your code inside, replacing `Solidity` statements.

### Writing Loops in Solidity

Loops are a fundamental part of control flow in programming—i.e., coding for eventualities like “if this, then that” or concurrent situations like “do this while doing that.” In most programming languages, loops are initiated with similar syntax. When it comes to loops, `Solidity` follows the same syntactic rules as `JavaScript` and `C`.

A `iterator` loop is an object that allows a programmer to traverse a container or list. Sometimes, iterators are used to instruct the computer to perform the same action a certain number of times, or to perform the same action on several elements in the code.

A generic loop has the same syntax in `JavaScript`, `C`, and `Solidity`. It instructs the computer to count from 0 to 10:

```
for (i = 0; i < 10; i++) {...}
```

If you looked closely at the list of opcodes in the previous chapter, you might have noticed that the `EVM` allows loops to be implemented in two ways. You can write loops in `Solidity`, or you can create them using the `JUMP` and `JUMPI` instructions. This jumps forward a specified number of steps in the program counter. Recall that the program counter tracks the number and order of computational steps as a given program executes on the `EVM`.

This is just one way that `Solidity` and `EVM` opcodes can be combined to create contracts that are both expressive, readable, and inexpensive to run. It should be noted that, due to how gas prices are calculated, certain functionalities might be easier to enforce or cheaper to execute if written using opcodes; this is particularly useful if you are writing your own language library.

If you have never seriously looked at code before and find the concept of loops difficult to understand, don’t worry for now; the following chapters will provide more background information.

### 表达力与安全性

`表达力强`（expressive）这一形容词在计算机科学中用于描述易于程序员编写和理解的代码。富有表达力的语言是人类思维模式与机器执行模式之间的桥梁。要使一种语言具备表达力，其各种结构必须直观易读，且其模板化代码（如关键字、特殊变量和操作码）应使用有助于程序员记忆其含义的人类可读词汇。

富有表达力的语言在被运行前必须被`编译`（compiled down）成更有利于机器的形式，这需要计算机完成相应工作。毕竟，富有表达力的语言通常更难进行推理（更难预测其行为），而更受限、更底层、抽象程度更低的语言则使这种推理变得更容易。

最终的前沿领域是既能轻松进行形式化验证，又能用例如`Solidity`这样富有表达力的高级语言编写的智能合约。这个问题亟待自动化解决，事实上，自动化形式化验证现在已近在眼前——这一事实必定让计算机科学家们兴奋不已，而以太坊开发者们将在不知不觉中从中受益。

## 形式化证明的重要性

如果你学习`Solidity`编程，可能会遇到其他开发者的好奇提问，他们会直击要点：你如何防止有人编写无限循环并锁死机器？

这绝非小众议题，而是与当今世界软件工程角色最相关的问题：`人类能否制造出一台其他人无法破坏的自由、开放可访问的虚拟计算机？` 如果答案是肯定的，那么它就公然挑战了公地悲剧理论。

### 共享全球资源的历史影响

在经济学中，`公地悲剧`（tragedy of the commons）是指共享资源无法长久维持的观点。最终，出于自身利益行事的用户会耗尽该资源，因为这样做对他们自身而言毫无成本。像这样一种某人可以中饱私囊或挥霍无度，同时将成本转嫁给其他人的情景，被称为`道德风险`（moral hazard）。

以下是一个例子：2016 年底，纽约市政府在下曼哈顿的街道上安装了计算终端。这些终端为过往行人提供免费 Wi-Fi。然而，这些终端还配有一个小型触摸屏，允许用户直接上网。这些共享资源刚一投入运行，人们就搬来椅子，观看 YouTube 或色情内容，并逗留数小时。⁴ 项目管理人员被迫迅速限制了屏幕上网功能，如今这些终端主要仅作为 Wi-Fi 热点使用。

因此，像以太坊虚拟机（`EVM`）这样极其廉价的公共计算机的概念堪称绝妙。任何人都可以随时随地用任何计算机访问它，并能在未来很长一段时间内运行程序。没有人拥有它，也没有人能篡改它。它甚至能为你存储资金。

### 攻击者如何破坏社区

去中心化经济体对世界各地各种私人既得利益构成了新兴威胁，尤其是在发展中经济体，在那里，有权有势的人更希望世界在没有解决公地悲剧方案的情况下继续运转（因此，仍受制于最新的独裁者或疯狂的暴民）。以太坊网络的安全性是第 7 章的主题。但以太坊的防御措施无处不在，甚至体现在编程语言本身之中，因此在此值得提及。

在此讨论中，你可以将`网络`（network）视为通过计算机相互连接的人们组成的社区。`攻击者`（attacker）则是憎恨这个群体，并想不惜一切代价给他们带来痛苦的人。

### 用 Solidity 编写的假设性攻击

假设一个攻击者想用一个用`Solidity`编写的、极度消耗内存的智能合约来锁死以太坊虚拟机（`EVM`）。攻击者愿意支付任意高昂的燃料费。（这是一个真实场景，你将在第 7 章中看到。）请记住，出于本例的目的，该合约也可以用为 `EVM` 创建的任何语言编写，例如`Serpent`，甚至更底层的 `EVM` 代码，而不仅仅是`Solidity`。

根据莱斯定理，某些计算机程序的行为属性在数学上是不可判定的，这意味着不可能编写另一个能够明确预测你向它展示的`Solidity`代码是否会终止的程序。⁵ 因此，无法编写任何有效的“守门员”程序来击退攻击者在此场景中编写的假设性内存密集型智能合约。

**注:** 智能合约与分布式应用程序（`dapp`s）不同，尽管两者都是分布式的且类似应用程序。`dapp`（分布式应用）是一种图形用户界面应用程序，它在后端使用以太坊智能合约，以取代传统的数据库和 Web 应用程序托管提供商。Dapp 可以通过 Mist 浏览器或 Web 访问。

以太坊虚拟机（`EVM`）通过各种方式应对这一现实，包括对每个区块的计算步骤数设定硬性限制、其确定性语言以及燃料费机制。尽管如此，如果存在经济激励，攻击者总会试图探索灰色地带，而在市值达到 10 亿美元的情况下，破解 `EVM` 并窃取以太币的激励是巨大的。

虽然灰色地带无法一次性通过工程手段消除，但可以通过随着时间的推移进行一系列协议分叉来应对。至于意外产生的破坏性程序，则需由以太坊社区来开发有利于编写直接、易于证明的合约的模式和实践，这些合约可以发展成为模板标准。第 5 章涵盖了其中一些最佳实践。

## 自动化证明来救场？

虽然不可能创建一个能剔除不良程序的守门员，但通过使用机器可检查的证明——一个能对其它程序进行数学证明的自动化程序——来生成可证明是正确的程序变得越来越可行。

由于智能合约涉及资金流动，它们成为自动化数学证明的绝佳试验品。计算机科学和数学研究这一领域的目标是，以一种系统化的方式，确保源代码满足特定的形式化规范。这是一种让独立审计人员介入并以数学方式验证程序是否确实按预期运行的方法。

自动化证明过程对企业来说是一大福音，但对学习`Solidity`的普通程序员来说作用不大。证明仅仅向你展示你`意图`发生的事情是否确实在程序中`发生`了。如果你的程序无法通过证明，自动化系统无法告诉你如何更好地编写它。

尽管如此，探讨这个主题的意义在于表明，以太坊网络有朝一日或许确实能够安全地承载大量自动化资金转移机器人，推动数万亿美元安全流动；并且开发这些机器人可能不会像今天这样缓慢、充满风险且晦涩难懂。

### 实践中的确定性

结合前面章节的概念，你会发现在某些方面，图灵完备的整个概念可能是一个理想化的概念，在设计现实世界的公共系统时实用性有限。

因此，也可以说在实践中，`EVM` 并非真正的图灵完备，因为 `Solidity` 合约执行的`有界性`可能很快就能从理论上预测 `EVM` 将运行的任何程序的行为。

`Bitcoin` 无法避免这些问题。表达性语言与机器语言之间存在的灰色地带同样存在于 `Bitcoin` 的脚本语言中，该语言也在运行时被编译为机器码。

### 失之毫厘，谬以千里

有趣的是，证明问题与本章前面讨论的`表达力`概念密切相关。人类只能在高级抽象语言上执行数学证明——即人类可读的编程语言，例如 `Solidity`。即便对于最专注的数学头脑来说，在汇编代码或机器代码上执行这样的证明也几乎是不可能的。

编译过程——将人类可读的代码转换为低级机器代码——牺牲了大量关于如何推理程序的信息（人类可解释的）。它也牺牲了对自动定理证明器有用的信息。因此，过程中总会引入一些歧义。如今，你永远无法完全确信，即使是经过数学证明的用 `Solidity` 编写的智能合约，在编译后仍然是可证明的。

## 测试，测试，再测试

防止歧义代码导致资金损失的方法是进行严格的测试。`Ethereum` 网络附带了一个名为 `Ropsten` 的测试网，它使用测试以太币，无需成本，并且可以像沙盒环境一样快速从水龙头获取。

实际上，`Ropsten` 与主链并无区别。它只是一个指定用于测试的不同链。就像`泰坦尼克号`及其姊妹船`不列颠尼克号`一样，它们除了名称外完全相同，其他任何人启动的链也是如此。这些链并无特殊或神圣之处；您将创建与它们完全一样的链，详见第 8 章。

### 命令行是可选工具！

请记住，`Ethereum` 的大部分重要功能都可以在 `Mist` 钱包中完成：发送和接收以太币、跟踪代币以及部署合约。对于打算学习编写去中心化应用（dapps）的开发人员来说，使用 `Geth`（或其他命令行客户端）是一个不错的选择。第 6 章会更详细地讨论 `Geth`。

在本节中，我们将简要地看一个真实的智能合约，探索一个关于如何使用智能合约的简单示例。

### 注意

如果您不会读写代码，请不用担心。本示例之后会有一个关于语法和结构的教程，帮助您理解代码的功能。在下一章中，我们将部署一个标准的 `Ethereum` 代币，且无需编写任何代码。

您将在第 5 章中学习如何部署这样一个合约。您会很高兴地了解到，在 `Solidity` 中部署一个简单合约只需要三个条件：

1. 一个文本编辑器，例如 `macOS` 上的 `TextEdit`、`Ubuntu` 上的 `Gedit` 或 `Windows` 上的 `Notepad`。请务必切换到纯文本模式，该模式会去除所有字体、下划线、粗体、超链接和斜体。（切勿使用富文本编写代码！）

2. 第 2 章中介绍的 `Mist` 钱包。

3. `Browser Solidity Compiler`，位于[`ethereum.github.io/browser-solidity/`](https://ethereum.github.io/browser-solidity/)，或可通过以下短链接访问：

```
http://compiler.eth.guide 
```

正如我们将在第 5 章中演示的那样，“上传”合约所需做的全部工作就是，将 `Solidity` 代码从文本编辑应用程序复制粘贴到 `Solidity Browser Compiler` 中。然后，将代码编译成字节码，再将字节码复制粘贴到 `Mist` 中。这真的非常简单，但让我们先不纠结于这些操作细节。相反，我们将审查下面示例智能合约的行为，以便您开始理解一个能收发资金的自动化合约的潜力。以下示例最初由 `Cyrus Adkisson`（GitHub 上的 `fivedogit`）编写，他是一位肯塔基州的软件工程师和 `Ethereum` 爱好者，现居纽约。此示例已根据本书内容进行了改编。

您将按照 `Solidity` 的命名约定，使用 `CapsCase`（而不是 `camelCase`）将此合约命名为 `PiggyBank`。您可以在[`solidity.readthedocs.io/en/develop/style-guide.html`](http://solidity.readthedocs.io/en/develop/style-guide.html) 找到这些命名约定以及 `Solidity` 风格指南的其余部分。

现在，让我们看看 `PiggyBank.sol`：

```
contract PiggyBank {

       address creator;
       uint deposits;

// Declaring this function as public makes it accessible to other users and smart contracts.
       function PiggyBank() public
       {
           creator = msg.sender;
           deposits = 0;
       }

// Check whether any ether has been deposited. When it is deposited, the number of deposits go up and the total count is returned

       function deposit() payable returns (uint)
       {
           if(msg.value > 0)
                deposits = deposits + 1;
           return getNumberOfDeposits();
       }

       function getNumberOfDeposits() constant returns (uint)
       {
           return deposits;
       }

// When the external account that instantiated this contract calls it again, it terminates and sends back its balance.
       function kill()
       {
           if (msg.sender == creator)
               selfdestruct(creator);
       }
}
```

您可以在[`solidity.eth.guide`](http://solidity.eth.guide) 找到更多适用于各种技能水平程序员的 `Solidity` 脚本示例。

### 格式化 Solidity 文件

前面的合约示例缺少一个主要细节。每个 `Solidity` 文件都应该（但并非必须）包含一个`版本编译指示（version pragma）`，这是一个声明，表明该合约是用哪个 `Solidity` 版本编写的。随着时间的推移，这应能防止旧版合约被未来版本的编译器拒绝。

此文件的版本编译指示是 `0.4.7`，因此您应该在文件头部添加以下内容：

```
pragma solidity ⁰.4.7;
```

有关 `Solidity` 文件结构的更多信息，请参阅[`solidity.readthedocs.io/en/develop/layout-of-source-files.html`](http://solidity.readthedocs.io/en/develop/layout-of-source-files.html) 。