# 第七章：使用 Solidity 进行智能合约编程

**引言：上一章学习内容回顾**

在上一章中，我们讨论了以太坊架构、生态系统和去中心化应用。我们还介绍了诸如 `MetaMask`、`Remix`、`Truffle` 和 `Geth` 等开发工具。在本章中，我们将学习用于智能合约和去中心化应用开发的详细 Solidity 编程技巧。

**什么是智能合约**

智能合约是一个含义过载的短语。尽管对智能合约有不同解释，我们对其定义如下：

>“智能合约是一种可执行的计算机程序，它运行在区块链上的以太坊虚拟机（`EVM`）中，用于访问和修改区块链块的状态。”

与通常运行在单个系统上的传统计算机程序不同，智能合约运行在区块链的每个节点上。

© 魏佳·张 和 泰杰·阿南德 2022  
W. 张 和 T. 阿南德，《区块链与以太坊智能合约解决方案开发》，[`doi.org/10.1007/978-1-4842-8164-2_7`](https://doi.org/10.1007/978-1-4842-8164-2_7)

第七章：使用 Solidity 进行智能合约编程

对于以太坊而言，智能合约被编译为可执行的字节码并部署到以太坊区块链上。智能合约的字节码通过交易的数据字段发送。一旦该交易被包含在一个区块中，就会生成一个智能合约地址，字节码则存储在该地址中。要调用智能合约中的函数，需向该智能合约地址发送一笔交易，并通过交易的数据字段提供函数名称和输入数据。当调用智能合约的交易被发送至某个节点时，以太坊虚拟机将从智能合约地址加载字节码并执行该函数。

对于商业人士而言，智能合约定义了业务属性和处理逻辑，使多方能够执行并见证业务逻辑的处理过程，从而确保对所有参与方（当智能合约编写良好时）的透明度、可靠性、容错性、不可篡改性和完整性。

智能合约与独立的计算机程序不同，因为它们存在于所有机器上，且所有参与方都能访问该智能合约，从而受益于区块链的透明度、可靠性及其他优势。

但这仅适用于智能合约编写良好的情况。如果智能合约编写不佳，则所有这些优势都将不复存在。



```markdown
对于技术人员而言，智能合约是一种计算机程序，可以在区块链上进行编码、编译、测试、部署和执行。

从技术角度来看，“智能合约”这个短语可能指代几种不同的事物。

首先，它可以指智能合约的源代码。其次，它可以指编译后的智能合约字节码。第三，它可以指部署在区块链上的智能合约。

当智能合约被编译时，会生成两个文件：一个字节码文件和一个`ABI`（应用程序字节码接口）。智能合约的字节码可以部署到以太坊区块链，并由以太坊虚拟机执行。`ABI`代码是对智能合约的定义和描述，而非可执行代码。第三方应用程序使用`ABI`文件来解析已部署的智能合约中定义了哪些函数，以及如何与它们交互。

当智能合约被“部署”到区块链时，会通过计算发送者地址和交易随机数（使用递归长度前缀（`RLP`）序列化编码）的`Keccak-256`哈希值来生成一个智能合约地址。智能合约也可以部署到不同的区块链，例如测试网或主网。同一个智能合约地址可能指向不同的区块链。

重要的是要知道，“智能合约”在不同的场景和上下文中被使用。在接下来的章节中，我们将详细介绍智能合约编程。

#### Solidity 编程语言是什么

`Solidity`是最流行的以太坊智能合约编程语言。`Solidity`源代码可以使用简单的文本编辑器或通过`Remix`或`Microsoft Visual Studio`等`IDE`进行编辑。为了让`Solidity`代码在以太坊节点上运行，需要完成几件事情。

首先，源代码需要被编译成可以被以太坊虚拟机（`EVM`）解释和执行的字节码。其次，你需要将智能合约字节码部署到区块链或`EVM`模拟器上以便执行。

字节码是一种可以由以太坊虚拟机执行的程序。字节码程序包含每一步的操作数、数据和存储。`EVM`引擎解释每一步的指令并执行这些指令。

如第[6](https://doi.org/10.1007/978-1-4842-8164-2_6)章所述，`Remix`是一个基于网页的集成开发环境（`IDE`），因此开发者可以在`remix.ethereum.org`这个`URL`指定的网页上编写代码。这对于练习智能合约的编写、编译和测试非常方便。第[6](https://doi.org/10.1007/978-1-4842-8164-2_6)章已经介绍了`Remix`，本节将重点介绍如何编写`Solidity`程序。

#### 模块 1：Hello World Solidity 示例

让我们来看第一个示例，`HelloWorld.sol`，如下所示：

```solidity
/**
 * *SPDX-License-Identifier: MIT*
 * *@title HelloWorld*
 * *@dev Implements the hello world program*
 */

pragma solidity >=0.7.0 <0.9.0;

contract HelloWorld {
    string helloworld = "Hello World";

    function justHelloWorld() public view returns(string memory) {
        return helloworld;
    }

    function showHelloWorld(string memory me) public view returns(string memory) {
        string memory result = string(abi.encodePacked(helloworld, " from ", me));
        return result;
    }
}
```

在这个`helloworld.sol`示例中，首先是一些注释，描述了该智能合约的内容，然后是实现了两个函数的`HelloWorld`智能合约。第一个函数`justHelloWorld()`简单地输出字符串“HelloWorld”，第二个函数接受一个名为“me”的输入变量，然后输出“HelloWorld from [me]”。这个简单的智能合约可以在区块链上编译、部署和执行。接下来，我们将描述智能合约`Solidity`代码的语法。

##### Solidity 注释
```


##### 注释在 Solidity 中的使用

注释在 Solidity 中被广泛使用，以确保程序员和需要使用并信任智能合约的第三方用户能够清晰理解智能合约。Solidity 支持两种注释。

第一种是双正斜杠 `//`，用于注释整行或行尾部分，如下所示。

```
// 这是注释整行
return "Hello World"; // 这是注释行尾部分
```

第二种语法是用 `/*` 和 `*/` 将文本括起来以注释整个段落。为了提高可读性，被注释段落中的每一行都可以以 `*` 开头。

```
/**
 * HelloWorld 程序演示了一个简单的智能合约，
 * 当调用 HelloWorld 函数时返回一个 "Hello World" 字符串。
 * 这是一个开源程序，无需许可即可复制或修改。
 */
```

##### Solidity 程序与版本声明

Solidity 源代码以 `pragma` 编译器指令开头，后跟 `solidity`，然后是版本规范。编译器指令是指导编译器解析并执行特定操作的指令。关键字 `pragma solidity` 指示编译器将源代码视为 Solidity 编程语言。

```
// pragma: 告知编译器编程语言和编译器版本
pragma solidity ⁰.5.2;

// 指定确切的次版本号
pragma solidity >0.5.2;

// 指定更新的版本
pragma solidity >0.5.2, <0.6.2; // 指定一个范围
```

Solidity 编程语言版本采用 `x.y.z` 格式，例如 `0.8.10`。其中，`x`、`y`、`z` 是可以包含 `0` 的数字。`x` 是主版本号，`y` 是次版本号，`z` 是补丁号。为了方便起见，由于 Solidity 中的 `x` 长期以来一直是 `0`，因此 `y` 被称为“主版本”，`z` 被称为次版本。

Solidity 语言版本部分可以有多种形式，例如以下：

```
~0.5.0   // 表示与 Solidity 精确匹配。
⁰.5.0   // 表示主版本匹配。此版本适用于任何 0.5.z，但不适用于 0.4.z 或 0.6.z。
>0.5.2;  // 指定更新的版本。此版本适用于任何比 0.5.2 更新的版本。
>=0.5.2, =<0.6.2; // 指定一个版本范围。可以选择任何大于或等于 0.5.2 但小于或等于 0.6.2 的版本。
```

Solidity 中的版本控制至关重要，因为 Solidity 程序在区块链上运行，一旦部署就无法更新或修补到更新的版本。

对于开发者来说，Solidity 版本可能会出现几个问题。由于智能合约无法升级或替换，因此确保源代码以其“最佳”版本编写非常重要，如下所示。

-   **确切的最新版本**：这是推荐的选项。但是，尽管建议指定确切的版本号，但在某些情况下需要灵活性以解决与其他库的兼容性问题。
-   **确切的旧版本**：当代码与最新的编译器不兼容，并且将其移植到最新编译器需要太多精力时使用。
-   **支持主版本**：这提供了更大的灵活性，特别是当代码范围有限并且可以作为各种版本智能合约的库使用时。
-   **指定版本范围**：当代码本身包含各种版本的库，并且智能合约并非关键任务时使用。

牢记 Solidity 编程正在不断发展，其语法也随着不同版本而变化，这一点非常重要。有时，开发者可能拥有适用于版本 `0.5` 的源代码，但在版本 `0.8` 下编译失败。因此，关注 Solidity 源代码或库的版本兼容性是一个好习惯。

##### 导入 Solidity 文件

在 Solidity 和版本声明之后，通常有一个 `import` 语句，用于将外部 Solidity 文件拉入源代码。



##### Solidity 基础

`Solidity` 是一种面向对象编程语言，且非常模块化。开发者将源代码分离到不同文件和库中，并将所需文件导入到主源代码中。

许多 `Solidity` 智能合约是开源的，开发者可以直接从开源仓库导入库。最流行的开源智能合约库之一是来自 `OpenZeppelin` 的库（[`github.com/OpenZeppelin/openzeppelin-contracts`](https://github.com/OpenZeppelin/openzeppelin-contracts)）。诸如 `SafeMath`、`ERC20`、`ERC721`、所有权和预言机等代码都可以从 `OpenZeppelin` 复用。

#### 导入源文件

要导入另一个源文件，只需使用以下格式：

- `import filename` – 这将从本地目录导入一个文件。
- `import full_filename_with_path` – 这将通过绝对路径导入一个文件。
- `import github_location` – 此语法将从 `GitHub` 位置导入一个文件。

你还可以导入文件并为其指定一个简单的引用名称，例如：

`import filename as XYZ`：这里，`XYZ` 可用于引用所导入的特定智能合约。

#### 构造函数

构造函数是一种在智能合约部署期间执行的特殊函数。它用于定义在部署阶段分配的参数，或运行算法以根据部署环境动态设置某些参数。

#### 函数修饰器

函数修饰器类似于宏，它被定义一次后可用于多个函数。例如，以下是一个检查交易发送者是否为所有者的修饰器。如果是，则验证通过；如果不是，则抛出异常。`_` 符号是函数代码将被插入并执行的位置。

```
modifier onlyOwner {
    require(msg.sender == owner);
    _;
}
```

```
function setMessage(string memory str) onlyOwner {
    //Do_Something
}
```

在上述代码中，`setMessage` 函数带有一个修饰器，用于检查发送者是否为智能合约的所有者。如果是，则 `Do_Something` 代码将在 `require` 语句之后的 `_` 位置执行。修饰器的最佳用途是简化智能合约中多个函数的冗余代码。一旦定义了修饰器，它就可以在任何地方使用。

#### 区块链访问范围：纯/视图/可支付函数

定义函数时有多种范围和权限。函数的范围描述了函数在数据是否可修改以及可访问哪些数据方面的边界。

- 如果函数被定义为 `pure`，则它是一个自包含函数。函数变量是局部的，函数不会访问区块链。它仅接收输入、处理并返回输出。`pure` 函数不依赖于区块链状态。
- 对于 `view` 函数，它可能访问区块链并从区块链获取一些只读数据以进行计算。`view` 函数不会改变区块链的状态。
- 对于 `payable` 函数，区块链状态可以被改变。对于任何将引发资产转移的函数，都应为该函数和地址分配 `payable` 类型。

关于 `pure`、`view` 和 `payable` 函数的一个常见问题是它们是否都消耗 `Gas` 费用。显然，`payable` 函数总是消耗 `Gas` 费用，因为它改变了区块链状态。对于 `view` 函数，它仅检索区块链数据，而 `pure` 函数甚至不访问区块链状态。关于 `pure` 和 `view` 函数是否消耗 `Gas` 的问题取决于函数的调用方式。如果 `pure` 和 `view` 函数是通过交易调用的，则交易会产生 `Gas` 费用，因为运行该函数会消耗 `CPU` 时间。如果 `pure` 和 `view` 函数不是通过交易调用的，则不会产生 `Gas` 费用。



#### 降低 Gas 费用的函数特性

没有任何 Gas 费用。可以通过直接连接客户端到以太坊节点、实例化本地节点中的智能合约对象并直接调用函数的方式来调用`pure`和`view`函数。直接调用智能合约函数不会生成交易。该操作可以由本地节点完成，而无需产生 Gas 费用。因此，关于`pure`或`view`函数是否消耗 Gas 的问题，答案取决于函数调用是否涉及交易。任何由交易触发的函数调用都会产生 Gas 费用。Gas 费用可以通过 EIP-1559 中的 Gas 费用消耗表进行计算。

#### 函数访问范围：`public`、`external`、`internal` 和 `private`

函数的访问范围定义了谁可以访问这些函数。这与`view`/`pure`/`payable`函数类型不同，后者定义了函数是否会访问区块链，以及如果访问，是读取还是写入区块链。

Solidity 函数有四种访问范围：`public`、`external`、`internal`和`private`范围。`public`函数意味着您可以从外部区块链客户端调用此函数。如果某个函数需要通过 Web3 客户端调用，则将其定义为`public`。`external`函数可以被另一个智能合约调用。Solidity 支持导入外部智能合约并调用在其他智能合约中定义的函数。调用外部智能合约的语法是`contractName.functionName()`。如果同一个智能合约调用`external`函数，请使用`this.functionName()`来引用同一合约内的调用。

`internal`函数可以被同一智能合约或派生智能合约中的其他函数调用。最后，`private`函数只能被同一智能合约中的函数调用，这是限制最严格的范围。

第七章 使用 Solidity 进行智能合约编程

#### 模块 2：Solidity 数据类型

数据类型是数据的一种支持格式和分类，它告诉编译器和执行引擎如何解释编程语言。与任何现代编程语言类似，Solidity 拥有一套良好的支持数据类型。通常，理解数据类型有助于程序员提高代码安全性和执行效率。对于 Solidity，了解和关注数据类型还有额外的原因。首先，调用智能合约函数的用户需要为计算步骤的存储和处理支付 Gas 费用。Gas 消耗可能相当昂贵，对于以太坊主网交易，有时每笔交易达到 200 美元。其次，智能合约函数对全世界开放。任何人都可以调用智能合约的`public`函数。如果变量的数据类型定义不当（例如分配了过多空间），恶意用户可能会向数据注入垃圾信息，导致性能问题甚至使智能合约函数调用的进程停滞。第三，与 Java 编程语言相比，Solidity 仅支持有限的数据类型。`double`和`float`等数据类型在 Solidity 编程语言中不受支持。

##### `bool`（布尔类型）

布尔是编程语言中最简单的数据类型。它仅使用一个存储位来表示变量的`true`或`false`状态。在 Solidity 中，可以使用`bool`关键字将变量声明为布尔类型，如下所示：

```solidity
bool new_member;
```

以下是一些可以应用于布尔数据类型的逻辑运算符：

- `!`（逻辑非）
- `&&`（逻辑与，即“且”）
- `||`（逻辑或，即“或”）
- `==`（等于）
- `!=`（不等于）

使用布尔数据类型时，感叹号`!`可以用于对布尔类型进行取反。取反操作将`true`状态变为`false`，将`false`状态变为`true`。对布尔类型的其他操作可以是`&&`或`||`。两个布尔类型的`&&`操作仅在两者都为`true`时为`true`。`||`操作则在至少一个为`true`时为`true`。



##### 布尔类型

对两个布尔类型变量进行或运算，如果其中任意一个变量为真，则结果为真。

两个布尔变量也可以进行比较。如果两个布尔变量同为真或同为假，则它们相等。有时，可以在 `if` 语句中对布尔变量进行条件判断。`If(variable)` 在变量为真时返回 `true`，在变量为假时返回 `false`。

由于布尔变量仅占用一个比特的存储空间，且存在多种可用于操作布尔变量的运算，建议开发者在可能的情况下优先使用布尔类型而非其他数据类型，以提高存储效率。

##### 整数类型

整数数据类型用于表示正整数（无符号）或任意整数（有符号）。在 Solidity 中，有符号或无符号整数可以有不同的尺寸，具体由分配给整数变量的比特位数决定。例如，`uint8` 表示一个 8 位无符号整数，其取值范围为 0 到 `2⁸-1`（即 0–255）。`uint256` 的取值范围为 0 到 `2²⁵⁶-1`。在 `uint8` 和 `uint256` 之间，还存在多种以 8 比特（1 字节）为增量递增的整数数据类型。有符号数据类型与无符号数据类型的不同之处在于，它可以表示正数和负数。例如，`int8` 的取值范围为 -128 到 127（即 `-2⁷` 到 `2⁷-1`）。`int256` 的取值范围为 `-2²⁵⁵` 到 `2²⁵⁵-1`。`int` 也可以有以 8 比特为增量步长的各种尺寸，例如 `int8`、`int16`、`int24`、`int32`，一直到 `int256`。

如果整数类型未指定比特位数，则默认为 256 位。`uint` 和 `int` 分别是 `uint256` 和 `int256` 的别名。

Solidity 还内置了用于整数类型变量的运算符。如下表（图 7-1）所示：

***图 7-1.** 整数数据类型的运算符*

由于智能合约可用于处理大量资产，确保数学运算完全准确至关重要。应对边界条件和输入的有效性进行彻底检查。许多库已被编写出来，以便开发者重用数学组件，并防止黑客通过数学函数中的安全漏洞攻击智能合约。例如，`SafeMath` 是一个用于处理数学运算的优秀库。开发者可以下载 OpenZeppelin 的 `SafeMath` 库，然后导入该智能合约以使用库中的类和函数。

##### 地址类型

地址是区块链中加密账户的唯一标识符。地址类型是一种在 C 或 Java 等传统编程语言中未定义的数据结构，但在智能合约编程语言中至关重要。以太坊区块链使用 20 字节的十六进制值来表示加密地址。

当你在以太坊中创建账户时，会生成三项内容。第一项是私钥，这是访问账户的唯一密钥，应由账户所有者保管，且绝不能向他人透露。第二项是公钥，用于代表该账户，可以公开。公钥可用于加密消息，且只有私钥才能解密该消息。第三项是从公钥派生出的地址。地址实际上是公钥的简化形式。只需对公钥应用 `Keccak-256` 哈希运算，然后取最后 20 个字节，输出结果即为该账户的地址。由于每个字节可表示两个十六进制数字，因此它实际上是一个 40 位的十六进制字符串，例如 `0x1F2D3A67b8E96039BbAC84EB4BC0913c0c16778c`。

在 Solidity 代码中，地址数据类型是资产账户的表示形式。在声明地址变量时，开发者需要指定该地址是否可以接收外部资金。这可以通过使用关键字 `payable` 来实现。

如果将地址声明为 payable，则该地址可以接收发送到该地址的资金。



`address`。否则，智能合约将拒绝任何发送到该账户的资金。`payable`关键字是一种额外的保护措施，以确保只有有效的账户才能接收转入的资产。

下面展示了如何定义一个不可支付和可支付的地址示例。

```
// address type is a twenty byte hex variable
address account_spending;
account_spending = 0x1F2D3A67b8E96039BbAC84EB4BC0913c0c16778c;
address payable account_receiving;
account_receiving = 0xDda897285Ce46CG78D786a9e993286AaC68c45bC;
```

在前面的示例中，定义了两个地址变量。第一个是`account_spending`，它是一个不可支付的账户。该账户不能用于接收任何资金，但可以从其自身账户发送资金。第二个名为`account_receiving`的变量具有`payable`属性，因此可以发送或接收该账户的资金。

另外需要指出的是，`address`不仅可以作为账户的标识，还可以指向一个智能合约实例。智能合约地址是已部署智能合约的入口点。此地址可用于表示已部署的智能合约，并调用该智能合约内部的函数。

**字节数组**

字节数组（图 7-2）使用数组来表示从 1 到 32 字节的固定长度字节。以下定义了各种类型的字节数组以及可应用于该数组的位运算。

*图 7-2. 字节数组数据类型*

注意：
`byte`等同于`byte1`。

要定义一个字节数组，请使用以下语法：

```
byte1 b1;
byte2 b2;
Byte32 b32;
```

在前面的示例中，`b1`、`b2`和`b32`分别代表一个 1 字节、2 字节和 32 字节的变量。

要检索字节数组变量`x`的元素，请使用`x[i]`，其中`i`的范围从 0 到字节大小，以获取索引`i`处的字节。

下图展示了字节数组的操作（图 7-3）。

*图 7-3. 字节数组操作*

**固定大小数组**

固定大小数组是任意数据类型的索引数组，例如：

```
data_type array_name[array_size]
```

此处，`data_type`是任何数据类型，如`uint`、`address`和`byte`，例如：

```
uint balances[30]; // 一个固定大小的无符号整数数组
address students[25];
// 一个地址数组，存储 25 名学生的账户地址
```

固定大小数组可以在声明期间通过赋值数据值来初始化，或者稍后通过为变量元素赋值来初始化。

```
uint balances[5] = [10, 20, 30, 40, 50]; // 声明一个无符号整数类型的 balances 数组，并为每个元素赋值。
uint balances[] = [10, 20, 30, 40, 50]; // 声明一个无符号整数类型的 balances 数组，并为每个元素赋值。数组大小被省略，其大小等于赋给数组的值的数量。
```

要为数组元素赋值，只需使用以下语法：

```
array_name[index] = data_value;
```

此处，`array_name`是数组变量名，`index`是数组元素的索引，`data_value`是赋给该元素的值。示例如下：

```
balances[3] = 300; // 将值 300 赋给 balances 数组的索引 3。
```

**动态大小数组**

在 Solidity 中，数组具有连续的内存地址。第一个元素指向最低地址，最后一个元素指向最高地址。对于固定大小数组，数组的大小在编译时设置。对于动态大小数组，数组大小在运行时设置。要声明一个动态大小数组，请使用以下语法：

```
data_type[] array_name;
```

此处，`data_type`是数据类型的名称，如`uint`和`address`。`array_name`是数组变量名，如`balances`和`students_addresses`。

```
uint[] balances;
address[] students_addresses;
```



##### 动态数组

声明动态数组时，需要在代码执行时为其设置一个长度值。

设置数组长度时，使用以下语法：

`array_name = new data_type[](size);`

此处，`array_name`是数组变量名，`data_type`是数据类型名，`size`是一个表示目标数组大小的`uint`类型值。例如，前面提到的数组变量可以设置如下长度：

```
*balances = new uint[](9); // 将 balances 数组大小设置为 9*
*students_addresses = new address[](10);*
*// 将 students_addresses 数组大小设置为 10*
```

##### 映射数据类型

除了`address`数据类型，另一个重要的类型是`mapping`类型。`Mapping`数据类型像关联数组一样关联两个变量。由于它能关联两个或多个变量，因此是一种重要的数据类型。

要声明映射类型，请使用以下语法：

`mapping(key_type => value_type)`

`key_type`可以是任何基础类型，例如`uint`、`address`、`bytes`和`string`等内置类型。`key_type`也可以是用户自定义或复杂类型，如合约类型、枚举、映射、结构体以及任何数组类型。`value_type`可以是任何类型，包括映射。

以下定义了一个将地址数组与每个数组元素的余额进行映射的映射：

```
*address student_address;*
*uint score;*
*mapping(student_address => score) public scores;*
```

上述示例将`student_address`定义为`address`数据类型，将`score`定义为`uint`类型。然后将`student_address`映射到`score`。要为某个学生分配成绩，请使用以下语法示例：

```
student_1_address = *0x1B2E2A67b8E96039BbAC84EB4BC0913c0c16668D;*
*score_1 = 90;*
*scores[student_1_address] = score_1;*
// 此示例将学生 1 的成绩设置为 90。
```

要获取学生 1 的成绩，只需将该学生的地址输入到`scores`变量中：

`scores[student_1_address] // 这将返回 90。`

`mapping`是一种多功能的数据类型，它能在没有预定义大小的情况下关联两个变量。这在智能合约中经常使用。

##### 枚举数据类型

`Enum`是一种数据类型，它枚举一个变量，使其只能拥有一些预定义的值。通过限制`enum`变量中的值，可以减少出错的可能性。

定义`enum`的语法是：

`enum enum_type_name{VALUE_LIST};`

这里，`enum`是数据类型关键字，`enum_type_name`是`enum`类型的名称。`VALUE_LIST`是一个由逗号分隔的值列表。

一旦定义了`enum`类型，就可以用它来声明一个`enum`变量：

`enum_type_name variable_name;`

例如，为了限制去中心化组织的角色，`role`变量可以是一个`enum`数据类型，如下所示：

```
*enum DAO_ROLES{SECRETARY, ACCOUNTANT, LEGAL, MEMBER};*
*DAO_ROLES newMember;*
*newMember = DAO_ROLES.MEMBER;*
```

在前面的示例中，`DAO_ROLES`被声明为一个枚举，它可以拥有值`SECRETARY`、`ACCOUNTANT`、`LEGAL`和`MEMBER`。然后使用`DAO_ROLES`声明了一个变量`newMember`。这个新成员被赋予了一个值`DAO_ROLES.MEMBER`。

通过使用`enum`，它可以将变量的值限制在一个有限的列表中，因此更不容易出错。

下面展示了一段代码片段，用以说明如何使用`enum`来定义变量：

```
*pragma solidity ⁰.6.0;*
*contract enum_example {*
	*enum DAO_ROLES{ SECRETARY, ACCOUNTANT, LEGAL, MEMBER};*
	*// 定义一个枚举类型*
	*DAO_ROLES latestMember; // 定义一个枚举变量*
	*function setRoleSECRETARY() public { // 声明一个函数*
	*将 latestMember 设置为 SECRETARY 角色*
		*latestMember = DAO_ROLES.SECRETARY;*
	*}*
	*function getRole() public view returns (DAO_ROLES)*
	*{ // 查询最新成员的角色*
		*return latestMember;*
	*}*
*}*
```

##### 结构体数据类型

`Struct`是结构体的缩写。与其他编程语言中的结构体类似，Solidity 支持`struct`数据类型来将多个变量组合在一起。这些变量可以拥有不同的类型。



##### 数据类型

结构体变量类型通过以下语法定义：

```
struct 结构体类型{
    数据类型 _1 变量 _1;
    数据类型 _2 变量 _2;
    ...
}
```

其中，`struct_type` 是要定义的结构体名称。`datatype_1`、`datatype_2` 等均为 Solidity 原生支持或用户自定义的数据类型。

以下展示了一个定义结构体的示例：

```
enum Experience{ENTRY, JUNIOR, SENIOR, EXPERT};
enum Skillset{SOLIDITY, PROTOCOL, BOTH};
struct Developer {
    address addr;
    Experience level;
    uint hourly_rate;
    Skillset skill;
}
Developer guru1;
```

在上述示例中，定义了一个名为 `Developer` 的结构体，它包含四个组成部分：开发者的账户地址、经验等级枚举、`uint` 类型的时薪以及 `enum` 类型的技能水平。变量 `guru1` 被声明为 `Developer` 结构体类型的变量。

若要访问结构体中的成员，需使用“`.`”符号。例如，为上述示例中的 `guru1` 赋值，应执行以下操作：

```
guru1.address = 0x1B2E2A67b8E96039BbAC84EB4BC0913c0c16668D;
guru1.level = Experience.EXPERT;
guru1.hourly_rate = 80;
guru1.skil = Skillset.SOLIDITY;
```

除了像上面这样逐一设置值之外，还可以通过结构体构造函数来为结构体变量赋值，如下所示：

```
guru1 = Developer(0x1B2E2A67b8E96039BbAC84EB4BC0913c0c16668D,
    Experience.EXPERT,
    80,
    Skillset.SOLIDITY
)
```

如果有多个开发者，例如 `guru1`、`guru2` 和 `guru3`，则可以使用映射与结构体的组合。例如，可以将一个 `uint` 映射到一个 `Developer` 结构体，以引用一个开发者列表：

```
mapping(uint=>Developer) developers;
```

在这里，`developers` 是一个映射变量。每个开发者可以分别被引用为 `developer[0]`、`developer[1]` 等。

映射与结构体的组合能够提供非常复杂的数据类型，可以解决 Solidity 编程中的大部分数据类型处理任务。

总而言之，Solidity 提供了一种以结构体形式定义新类型的方法。以下代码片段展示了包含地址和捐款金额的 `Donor` 结构体示例：

```
contract Charity {
    // 定义一个包含两个字段的结构体
    struct Donor {
        address addr;
        uint donation_amount;
    }
    // 定义一个映射
    mapping (uint =>Donors) donors;
    //...
}
```

##### 区块链特定变量

智能合约经常需要从区块链本身获取信息，并在函数中使用区块链数据。Solidity 实际上定义了一些全局变量来引用区块链状态和交易数据。常见的全局变量和函数如下表所示（图 7-4）。主要包含两个主要变量：`msg`（消息全局变量）和 `block`（区块链变量）。

***图 7-4.** 特殊的区块链变量与函数*

上表中列出的大多数全局变量是自解释的。但有少数几个变量需要进一步澄清。

`msg` 变量主要定义了交易中的参数和数据。每次向以太坊区块链发送交易时，智能合约都可以引用 `msg` 对象来提取以下信息：

- `msg.value` —— 这是发送给接收方的以太币数量，以 wei 为单位。
- `msg.data` —— 这是交易中的数据字段，是发送到智能合约进行处理用户输入数据。
- `msg.sig` —— 这是发送交易者的签名。
- `msg.sender` —— 这是发送者的地址。检查发送者地址非常重要，以确保发送者有权对智能合约函数执行操作。
- `block.blockhash` —— `block.blockhash` 是一个特殊函数，它接受一个区块编号并输出该区块的哈希值。
- `block.difficulty` —— 输出区块链挖矿的难度。



##### Solidity 中的全局变量与事件

##### 全局变量

`block.gaslimit` – 定义最新区块的燃料限制。

`block.number` – 最新的区块编号。

`gasleft()` – 返回交易剩余的燃料量。

`now` – 当前时间戳。

另一个全局变量是 `tx`，专门用于表示交易。

`tx.gasprice` – 显示交易的燃料价格。

`tx.origin` – 交易的原始发送者。在单次函数调用中，它与 `msg.sender` 相同。

#### 模块 3：事件

##### 什么是以太坊事件

以太坊事件是重要的概念，与如何传递智能合约状态以及如何与外部程序通信相关。事件类型是 Solidity 编程语言内置的一种智能合约可继承成员。Solidity 提供了定义事件格式和触发事件的语法。

##### 事件存储在何处

一旦事件被触发，相应的事件数据就会存储在交易日志中。事件数据是传递给 `emit` 事件函数的参数列表。外部程序可以通过智能合约地址访问这些交易日志。尽管事件存储在交易日志中，但其内容对智能合约是不可访问的。智能合约可以触发事件，但不能访问已触发的事件。

##### 如何定义事件

定义事件非常简单：只需使用 `event` 关键字，配合一个事件名称和一系列属性即可，如下所示：

```
event eventName(dataType_1 [indexed] attribute_1, dataType_2 [indexed] attribute_2, ..., dataType_n [indexed] attribute_n);
```

其中：
- `event` 是定义事件的关键字。
- `eventName` 是事件的名称。
- `dataType_1`、`dataType_2`、`dataType_n` 是在 Solidity 中定义的数据类型列表。
- `attribute_1`、`attribute_2`、`attribute_n` 是由开发者指定的属性名称列表。
- `indexed` 是一个保留关键字，允许使用带索引的参数作为过滤器来搜索这些事件。

例如，要定义一个代币铸造事件，请使用以下事件定义示例：

```
event Mint(address indexed receiver, uint amount);
```

在上述示例中，定义了一个包含两个属性的 `Mint` 事件。第一个属性是铸造代币接收者的地址。该地址被设置为索引，意味着这是一个可搜索的属性。第二个属性是铸造的代币数量。该事件无需指定代币名称或铸造时间，因为这些信息可以从智能合约地址和交易的区块时间推断出来。

总之，事件定义基本上就是定义一个事件名称和一系列属性。事件一旦定义完成，就可以通过传递参数来触发并发出事件。

##### 如何触发事件

事件类型在定义后，即可被触发并记录到交易日志中，该过程由智能合约函数控制。触发事件的语法如下所示：

```
emit eventName(parameter_1, parameter_2, ..., parameter_n);
```

其中：
- `emit` 是用于触发事件的关键字。
- `eventName` 是开发者使用 `event` 关键字定义的事件类型。
- `parameter_1`、`parameter_2`、`parameter_n` 是事件类型中定义的属性对应的参数。参数的数据类型应与事件属性的数据类型匹配。

一个事件类型可以根据需要被多次调用以触发多个事件。例如，上一节定义的 `Mint` 事件可以在每次执行铸造操作时触发一个事件。

```
emit Mint(0x1F2D3A67b8E96039BbAC84EB4BC0913c0c16778c, 200);
```

在上述示例中，触发了一个事件，表明已有 200 个代币被铸造并发送到指定地址 `0x1F2D3A67b8E96039BbAC84EB4BC0913c0c16778c`。

当事件被触发时，其数据实际上会被保存到交易日志中。可以通过使用区块链浏览器查看交易的日志部分来观察该事件。该日志可以被访问。



通过智能合约的地址访问。日志不能在智能合约内部被访问。智能合约不能在一个函数中发出事件，然后调用另一个函数来处理发出的事件。事件只能通过外部程序（如客户端程序）访问。`Web3`库有一些事件访问函数调用，可用于检索或搜索事件。

**事件示例**

一旦我们了解了事件的定义和发射，编写事件就非常简单。下面展示了一个`DepositEvent`合约，它发出存款记录：

```
pragma solidity ⁰.8.0;

contract DepositEvent {
    event Deposit(address indexed depositor_address, uint indexed deposit_id, uint deposit_amount); // 定义了一个 Deposit 事件，其属性为存款人地址、存款人 ID（或存款编号）和存款金额

    function deposit(uint deposit_id) public payable {
        emit Deposit(msg.sender, deposit_id, msg.value); // 发射一个 Deposit 事件，其发送者地址、deposit_id 和 deposit_amount 等于交易中转移的以太币。
    }
}
```

在这个例子中，定义了一个`DepositEvent`类的片段。在该类中，全局定义了一个`Deposit`事件。有一个`deposit`函数，它接收`deposit_id`作为输入，并发出一个包含`depositor_address`、`deposit_id`和`deposit_amount`的事件。`depositor_address`和`deposit_amount`直接从特殊变量`msg`中获取。

事件是一种允许智能合约与外部程序通信的传递方法。区块链是高度自封闭的，智能合约不能直接访问外部程序。在这种情况下，事件成为外部程序和智能合约之间的消息传递机制。由于事件存在于交易日志中，而交易日志是以太坊状态的一部分，因此发出事件会导致消耗 gas，应谨慎设计。

**模块 4：安全**

安全是 Solidity 编程最重要的方面。在第[8](https://doi.org/10.1007/978-1-4842-8164-2_8)章中，我们用了整整一章描述区块链安全。在本模块中，我们将讨论 Solidity 编程中的安全性。

**函数漏洞**

**函数可见性错误**

这是一种漏洞，即函数可见性未被指定，或者本应是私有的函数被指定为`public`。在早期版本的 Solidity 编译器中，函数的可见性默认为`public`。函数应被正确指定为`external`、`public`、`internal`或`private`。

以下示例展示了这种漏洞的一个例子。第一个函数有一个输入检查，并调用第二个函数。第一个函数被声明为`public`，并且需要由客户端应用程序接收用户输入来调用。第二个函数存在漏洞，因为它被声明为`public`，但在没有检查发送者有效性的情况下就发送了资金。

```
pragma solidity ⁰.4.24;

contract HashForEther {
    function withdrawWinnings() public {
        // 如果地址的最后 8 个十六进制字符为 0，则为赢家。
        require(uint32(msg.sender) == 0);
        _sendWinnings();
    }

    function _sendWinnings() public { // 安全错误。此函数应声明为 private
        msg.sender.transfer(this.balance);
    }
}
```

要修复此漏洞，只需将第二个函数声明为`private`。

**漏洞：未检查函数调用的返回值**

这是一种漏洞，即未检查函数调用的返回值。当调用一个函数并返回错误时，后续程序仍然执行。检查返回值、异常并相应地处理返回值非常重要。

**漏洞：以太币提现操作未受保护**

这是一个严重的漏洞。它可能在许多情况下发生。提现功能应受到多方面因素的保护。首先，函数的可见性应该是正确的。其次，输入的地址



需要检查以确保发送者有权提取资金。此外，构造函数代码需要得到保护。构造函数在运行时字节码中运行，可以被黑客调用以执行代码。

**漏洞：自毁函数**

这是一个漏洞，用户或黑客可以调用函数来销毁智能合约的功能，使其无法恢复。这发生在著名的 Parity“我不小心杀死了它”的漏洞中，一名匿名用户调用了 Parity 多重签名钱包组件中的`kill`函数并销毁了该组件。该漏洞导致总计 513,774.16 个以太币无法被资产所有者访问。为了防止这种情况发生，应尽量减少使用`kill`函数、放弃所有权函数和`destruct`函数，除非绝对必要。自毁函数包括`suicide`或`selfdestruct`函数。

```
// This is a selfdestruct function that will remove the
// contract and send the remaining asset to the sender address
pragma solidity ⁰.4.22;

contract SimpleSuicide {
    function sudicideAnyone() {
        selfdestruct(msg.sender);
    }
}
```

**漏洞：使用 Solidity 已弃用函数**

某些旧版 Solidity 中的函数已被弃用并替换为新函数，如图 7-5 所示。编译时，请关注警告并用新函数替换已弃用的函数。

***图 7-5.** Solidity 中的已弃用函数*

**漏洞：`Delegatecall`到不可信的被调用方**

Solidity 支持`delegatecall`函数，该函数将使用调用合约的相同执行上下文调用另一个智能合约。这意味着`msg.sender`和`msg.value`对于调用方和被调用方是相同的。如果被调用方地址不可信，则会导致调用合约的安全问题。

**漏洞：由失败函数调用导致的拒绝服务**

外部调用可能失败，且不应停止其余执行步骤。开发者应避免在单笔交易中组合多个调用。合约函数应具有处理失败调用的逻辑。在向用户发送资金时，最好让用户通过发起交易来“拉取”资金，而不是使用智能合约将资产推送给一组用户。

**漏洞：竞态条件和交易顺序依赖**

区块链不会按提交顺序执行交易。交易由矿工打包，矿工倾向于打包具有较高燃料费的交易。当不同智能合约中的函数调用存在依赖关系时，这会导致某些智能合约函数出现问题。例如，ERC20 同质化代币有一个`approve`函数，用于指定另一个用户使用一定数量的代币。然后有一个`transfer`函数，可以转移不超过已批准金额的资金。假设 Alice 批准 Bob 使用金额为`m`的资金，然后更改批准金额为`n`，在此期间，Bob 发送了两笔交易，分别转移金额`n`和`m`。由于交易并非严格按照顺序执行，Bob 的第一笔转账可能落在 Alice 的第一次批准和第二次批准之间，而 Bob 的第二笔转账则落在第二次批准之后。这将允许 Bob 提取`m + n`个代币，而不是预期的`n`或`m`金额。

修复函数调用中的竞态条件并不容易。确保函数依赖关系的一种方法是使用秘密盐值和哈希方法。发送者将拥有一个秘密盐值，并生成该盐值的哈希值。然后，函数发送者将使用哈希值和地址调用已批准的函数。智能合约将把哈希值保存到区块链。发送者随后将盐值发送给接收者。接收者随后调用请求。



#### 智能合约安全漏洞

##### 带秘密盐值的函数

函数使用一个秘密盐值。智能合约计算该盐值的哈希值，并与智能合约保存的哈希值进行比较。如果匹配，请求函数将处理接收者的请求；如果不匹配，请求将被拒绝。这确保了后续两次函数调用的一一对应关系。

##### 漏洞：断言违反

`assert()` 是一个函数调用，用于确保被求值的语句始终为真。这与用于检查语句条件的 `require()` 函数不同。`assert()` 的结果绝不应该为 `false`。如果 `assert()` 函数返回 `false` 结果，则意味着代码中存在严重错误。

##### 漏洞：跨合约调用陷入循环

Solidity 智能合约支持调用另一个智能合约。如果两个智能合约相互调用对方的功能，调用有可能陷入循环并消耗所有资金，如下例所示：

```solidity
// security_callee.sol:
pragma solidity 0.8.0;

contract Bob {
    function ping(address c) public {
        //do something
        return;
    }
}

contract Mallory {
    fallback() external {
        Bob(msg.sender).ping(address(this));
    }
}
```

```solidity
// security_caller.sol
contract Bob {
    bool sent = false;
    
    function ping(address c) external {
        if (!sent) {
            c.call{value:2}("");
            sent = true;
        }
    }
}
```

上述示例展示了跨智能合约调用如何可能陷入循环。

在这个例子中，`security_callee.sol` 和 `security_caller.sol` 都被部署到区块链上。`security_caller.sol` 中的 `Bob` 合约调用一个名为 `ping` 的函数，该函数接收一个来自外部程序的输入。`ping` 函数随后使用 `Mallory` 合约地址调用另一个智能合约。`Mallory` 合约具有一个回退函数，该函数会接收来自 `Bob` 合约的 `ping` 函数调用。在 `Mallory` 合约中，`Bob` 合约也被调用，从而导致一个循环：`Bob` 调用 `Mallory` 中的回退函数，而 `Mallory` 又调用 `Bob` 的 `ping` 函数。循环中的每一步都会向 `Mallory` 合约发送 2 wei 的价值。

##### 数据类型与数据漏洞

##### 漏洞：变量值溢出或下溢

当算术运算导致变量的新值超过最大值或低于最小值时，就会发生这种情况。

```solidity
uint256 const PRICE_PER_TOKEN = 2;

function buy(uint256 numTokens) public payable {
    require(msg.value == numTokens * PRICE_PER_TOKEN);
    balanceOf[msg.sender] += numTokens;
}
```

要修复此漏洞，请检查值的范围。如果可能，请使用 `SafeMath` 库，该库会检查算术运算的边界。

##### 漏洞：状态变量被遮蔽

对于智能合约，存在状态变量和函数特定变量。状态变量也可以在每个函数中引用。如果一个变量名同时被定义为状态变量和函数变量，那么函数中定义的变量将具有优先权，并遮蔽被定义为状态变量的变量。因此，检查函数中变量的上下文和作用域非常重要。以下智能合约展示了状态变量如何在函数中被遮蔽：

```solidity
pragma solidity 0.8.0;

contract ShadowingVariables {
    uint n = 2;
    address public x = 0x1f2D3A67B8E96039bbAc84eB4bC0913C0c16778c;

    function test_shadow1() public view returns (uint n) {
        return n; // 将返回 0
    }

    function test_shadow2() public view returns (address x) {
        address x = 0x1111111111111111111111111111111111111111;
        return x; // 将返回 0x1111111111111111111111111111111111111111
    }

    function test_shadow3() public view returns (address x) {
        return x; // 将返回 0x0000000000000000000000000000000000000000
    }
}
```

在该智能合约中，声明了状态变量 `uint n` 和 `address x`。`test_shadow1` 函数将 `n` 声明为返回变量。它将



`return 0`，它未被赋值，将采用默认值 0。

第二个函数 `test_shadow2` 重新定义了地址 `x`，并分配了一个新地址 `0x1111111111111111111111111111111111111111`，并将返回该地址。

第三个函数 `test_shadow3` 重新声明了地址 `x`，但未分配地址值。因此，它将返回默认地址值 `0x0000000000000000000000000000000000000000`。

**重要**的是要关注同一变量名出现在不同上下文中的情况，并确保与该变量关联的是正确的值。

##### 漏洞：通过 `tx.origin` 进行授权

Solidity 智能合约可以使用 `tx.origin` 作为全局变量来引用交易的原始发送者。由于用户或智能合约可能调用恶意的智能合约函数，因此使用 `tx.origin` 进行身份验证并非良好实践。相反，应使用 `msg.sender` 进行身份验证，因为它总是调用另一个智能合约的智能合约的真实地址。例如，在以下编程示例中，使用 `tx.origin` 来检查发送者是否是智能合约的所有者。这可能存在漏洞，因为 `tx.origin` 账户可能调用一个恶意的智能合约，然后调用此 `sendTo` 函数，从而绕过必要的检查，将资金发送给黑客指定的接收者。

```
pragma solidity 0.4.24;

contract MyContract {
    address owner;

    function MyContract() public {
        owner = msg.sender;
    }

    function sendTo(address receiver, uint amount) public {
        require(tx.origin == owner); // 这应改为 tx.sender
        receiver.transfer(amount);
    }
}
```

##### 漏洞：使用区块值表示时间

一些智能合约需要处理依赖于时间的操作，例如资产锁定或释放。Solidity 有一些特殊的全局变量，例如 `block.timestamp` 和 `block.number`，可用于表示或推断经过的时间。然而，由于区块挖矿并不精确，并且可能被矿工操纵，因此不建议将有时间依赖性的函数中的区块参数用作时间戳。有时，使用来自预言机实现的时间信息会更好。

##### 漏洞：写入任意存储位置

以太坊 EVM 为每个账户或智能合约地址将数据存储在持久化位置。保护数据存储位置免受恶意覆盖非常重要。虽然 Solidity 不支持位置指针，但仍然有可能将数据写入错误的地址。例如，在动态数组中，如果未正确设置数组长度，则将不会检测到越界索引，从而导致越界写入有效。在下面的例子中，定义了一个动态长度数组 `bonusRecord`。`PopBonus` 函数每次弹出一个项目，并减少 `bonusRecord` 数组的长度。但是，由于 `require(0 <= bonusRecord.length)` 并未阻止长度为 0 的情况，下一行代码 `bonusRecord.length--` 将导致下溢，使 `bonusRecord` 长度变为 0564039457584007913129639935。

由于数组长度如此之大，数组索引可以是任何数字，并且该值可以写入任意存储位置：

```
uint[] private bonusRecord;
address private owner;

function PushBonus(uint c) public {
    bonusRecord.push(c);
}

function PopBonus() public {
    require(0 <= bonusRecord.length);
    // 这是一个错误。一旦长度为零，就不应允许 PopBonus 操作
    bonusRecord.length--;
}

function UpdateBonusRecordAt(uint idx, uint c) public returns (uint){
    require(idx < bonusRecord.length);
    bonusRecord[idx] = c;
    return bonusRecord.length;
}
```

要修复上述任意写入问题，只需将 `require(0 <= bonusRecord.length)` 更改为 `require(0 < bonusRecord.length)`。

##### 漏洞：未使用的变量



开发者声明了变量却未使用，这是相当常见的情况。

在 Solidity 中，所有计算和存储都会消耗交易的 Gas。因此，最佳实践是移除所有已定义但未在已部署的智能合约中使用的函数和变量。

##### 编译器漏洞

##### 过时的编译器

编译器版本兼容性是一个复杂的问题。Solidity 允许将智能合约声明为适用于单个编译器版本或一系列编译器。虽然建议使用最新的编译器，但与旧版本库的兼容性是一个挑战。有时，库是由第三方编写的，或者属于公共领域，将它们重写为最新版本并不容易。开发人员应同时查看编译错误和警告，以确保版本兼容。

使用 Solidity 编程智能合约（第 7 章）

##### 随机性漏洞

##### 漏洞：来自区块链属性的弱随机性

智能合约不直接与外部程序通信，也缺乏良好的随机数生成来源。有时，在游戏或彩票等应用中，需要使用随机数。开发人员需要知道，某些区块链属性并不像看起来那么随机。例如，以太坊矿工可能通过控制区块生成时间或打包不同的交易来操纵 `block.timestamp` 或 `blockhash`。

对于需要高随机性的应用，建议使用外部随机数生成器和预言机将随机性引入智能合约。

##### 签名漏洞

##### 漏洞：签名篡改

有时，智能合约会实现加密函数来验证签名的消息，并据此执行转账操作。为了确保安全，智能合约函数需要确保签名的消息是真实的，并且消息不能被重放。当使用消息时，私钥和消息作为输入来创建签名。需要注意的一个重要点是，签名不是唯一的。黑客可以操纵 `(r,s,v)` 参数，为相同的私钥和消息创建不同的但有效的签名。因此，签名或哈希不能用作消息交易的唯一标识符。否则，黑客可以利用这一点，通过创建不同的有效签名来重放先前签名的消息。

例如，以下 `msgid` 不是唯一的，不应用作签名消息的标识符：

使用 Solidity 编程智能合约（第 7 章）

```
bytes32 msgid = keccak256(abi.encodePacked(getTransferHash(_to,
_value, _gasPrice, _nonce), _signature));
require(!signatureUsed[msgid]);
```

此处，对于由同一私钥签名的相同消息，`msgid` 本应是唯一的。然而，由于 `_signature` 不是唯一的，`msgid` 可以变得不同，因此相同的签名消息可以被多次重放以触发其他可能涉及资产转移的操作。

要解决这个问题，只需在哈希函数中移除 `_signature`，使 `msgid` 唯一，从而阻止消息的重放。

```
bytes32 msgid = keccak256(abi.encodePacked(getTransferHash(_to,
_value, _gasPrice, _nonce)));
require(!signatureUsed[msgid]);
```

#### 模块总结

安全是智能合约开发中最基本的组成部分。为了确保安全，应该建立安全框架，并且开发人员还应关注每一行代码的细节。第 [8](https://doi.org/10.1007/978-1-4842-8164-2_8) 章将对安全性进行更多讨论。

#### 模块 5：工具、测试与调试

在第 [1](https://doi.org/10.1007/978-1-4842-8164-2_1) 章中，我们讨论了基本工具，例如使用 Truffle 和 Remix 搭建智能合约开发环境来编译和部署智能合约。在本模块中，我们继续介绍有用的工具，例如智能合约可视化工具、安全扫描工具，


