# 第 3 章 Solidity

##### 3.4.8.2 视图函数

在 Solidity 中，`view` 函数是一种只读取合约状态变量而不对其进行修改的函数。

如果 `view` 函数中存在以下任意语句，编译器会认为它们正在修改状态变量并给出警告：

-   修改/覆盖状态变量。
-   创建新合约。
-   调用非 `pure` 或 `view` 的函数。
-   触发事件。
-   在内联汇编中使用特定操作码。
-   使用 `self-destruct`。
-   使用低级函数调用。
-   在函数调用中发送以太币。

下面是一个示例：

```solidity
pragma solidity ⁰.8.13;

contract Sample {
    // 状态变量
    uint x = 10;

    // 定义一个视图函数
    // 它返回两个作为参数传入的数值与状态变量 x 的乘积
    function calculateProduct(uint y, uint z) public view returns(uint) {
        uint product = x * y * z;
        return product;
    }
}
```

正如我们在代码中所见，我们以只读方式使用了状态变量 `x`，并未对其进行修改。

##### 3.4.8.3 纯函数

在 Solidity 中，`pure` 函数不会读取或更改状态变量。相反，它们仅使用传递给函数的参数或其拥有的任何局部变量来计算值。如果 `pure` 函数中存在读取状态变量、访问地址或余额、访问像 `block` 或 `msg` 这样的全局变量、调用非 `pure` 函数等语句，编译器将会发出警告。

例如：

```solidity
pragma solidity ⁰.8.13;

contract Sample {
    // 状态变量
    uint x = 10;

    // 定义一个纯函数
    // 它返回两个作为参数传入的数值的乘积
    function calculateProduct(uint y, uint z) public pure returns(uint) {
        uint product = y * z;
        return product;
    }
}
```

我们可以看到，我们只使用了参数来计算乘积；函数内部甚至没有读取状态变量。

##### 3.4.8.4 回退函数

Solidity 中的回退函数是一个没有名称、参数或返回值的 `external` 函数。例如，回退函数负责在以太坊区块链的智能合约中发送、接收和持有以太币。在以下任一情况下，它会被执行：

-   当函数标识符与智能合约的任一函数都不匹配时
-   当函数调用未附带任何数据时

回退函数具有以下属性：

-   它们是匿名函数。
-   它们不能接受参数。
-   它们不能返回任何值。
-   一个智能合约中只能有一个回退函数。
-   必须将其标记为 `external`。
-   应将其标记为 `payable`。如果没有标记，合约在收到不带任何数据的以太币时会抛出异常。
-   如果由其他函数调用，其 gas 限制为 2300。

目前不必担心 gas 方面的内容。待我们了解以太坊后，再详细讨论。

例如：

```solidity
pragma solidity ⁰.8.13;

contract Sample {
    uint public z ;

    fallback () external { z = 100; }
}

contract Invoker {
    function callSample(Sample sample) public returns (bool) {
        // 这里我们调用了一个不存在的函数
        (bool success,) = address(sample).call(abi.encodeWithSignature("hello()"));
        require(success);
        // sample.z 现在是 100
    }
}
```

这里，我们可以看到调用者合约试图在 `Sample` 合约上调用一个名为 `hello` 的函数。由于 `Sample` 合约中不存在名为 `hello` 的函数，因此调用了回退函数，将状态变量 `z` 的值设置为 100。

##### 3.4.8.5 Solidity 中的函数重载

函数重载允许我们在同一个合约中拥有多个同名函数。这只有在函数的类型或参数定义不同的情况下才可能实现。

以下是一个函数重载的示例：




pragma solidity ⁰.8.13;

```
contract Sample {

    function calculateProduct(uint x, uint y) public pure returns(uint){
        return x*y;
    }

    function calculateProduct(uint x, uint y, uint z)
    public pure returns(uint){
        return x*y*z;
    }

    function doProductUsing2Arguments() public pure
    returns(uint){
        return calculateProduct(4,5);
    }

    function doProductUsing3Arguments() public pure
    returns(uint){
        return calculateProduct(4,5,6);
    }
}
```

在上述所有例子中，我们都看到了合约的使用。在此，我们定义什么是 Solidity 合约。

可以将 Solidity 中的合约视为类似于 C++ 中的类。Solidity 中的合约包含以下内容：

1. 构造函数
2. 状态变量
3. 函数

## 第 3 章 Solidity

函数和状态变量有不同的可见性要求。这些要求通过可见性限定符来实现，如下所示：

1. **External** – 外部函数由其他合约调用。它们不能在合约内部被调用。如果要从当前合约内部调用外部函数，则需要使用函数名称进行调用。

2. **Public** – 这些函数既可以从同一合约内部调用，也可以从其他合约调用。Solidity 会为每个公共状态变量自动生成一个 getter 函数。

3. **Internal** – 与公共函数相反，内部函数的可见性限定在定义该函数的同一合约内，或定义该内部函数的父合约的子合约内。

4. **Private** – 私有函数和变量只能在同一合约内使用。即使是子合约也不能使用它们。

Solidity 合约可以通过使用继承的概念来扩展功能。下面是一个简单的继承示例：

```
pragma solidity ⁰.8.13;

contract Sample {

    constructor() public {
    }

    //我们定义一个公共函数来演示继承
    function getProduct(uint p, uint q) internal pure returns (uint) { return p * q; }
}

//派生合约
contract ChildSample is Sample {
    uint private res;
    Sample private samp;

    constructor() public {
        samp = new Sample();
    }

    function getProductOfTwo() public {
        //调用父合约的 getProduct 函数
        res = getProduct(6, 7);
    }
}
```

#### 3.4.9 抽象合约

如果一个合约至少有一个未实现功能的函数，那么该合约就是抽象的。这种合约被用作一个起点。大多数情况下，抽象合约既包含已实现的函数，也包含抽象函数。派生合约在需要时会使用现有函数，并实现抽象函数。如果派生合约没有实现抽象函数，它将被标记为抽象。

示例如下：

```
pragma solidity ⁰.8.13;

abstract contract Sample {
    function getProduct() virtual public view returns(uint); 
}

contract SimpleSample is Sample {
    function getProduct() public override view returns(uint) {
        uint x = 10;
        uint y = 11;
        uint product = x*y;
        return product;
    }
}
```

在这段代码中，我们定义了一个抽象合约 `Sample`，其中包含一个函数 `getProduct`。请注意，按照预期，`getProduct` 函数在抽象合约 `Sample` 中并未实现。

#### 3.4.10 接口

接口类似于抽象合约，并且使用 `interface` 关键字来创建它们。以下是接口最重要的部分。可以将接口视为一种合约，其实现方式由实现者决定。例如，`ERC20` 接口定义了一组方法，然后可以在以太坊区块链上创建代币时实现这些方法。一个合约可以继承自一个或多个接口。因此，可以使用多个接口组合出一个实现。以下也是事实：

- 接口不能有已实现的函数。
- `External` 是唯一可以作为接口一部分的函数类型。
- 接口中不能有`constructor`（构造函数）。
- 接口中不能有任何状态变量。
- 使用接口名称加点的表示法，可以访问作为接口一部分的枚举和结构体。

例如：


```solidity
pragma solidity ⁰.8.13;

interface Sample {

    function getProduct() external view returns(uint);

}

contract SimpleSample is Sample {

    function getProduct() public view returns(uint) {

        uint x = 10;

        uint y = 11;

        uint product = x*y;

        return product;

    }

}
```

除了抽象合约和接口，Solidity 还支持**库**的概念。

#### 3.4.11 库

库与合约类似，但它们主要用于重复使用。库包含可被其他合约调用的函数。使用 Solidity 库时需要遵循一些规则。以下是关于 Solidity 库的几项要点：

- 如果库函数不改变状态，则可直接调用。
- Solidity 库旨在无状态，因此库不能包含任何状态变量。
- 就继承而言，库不能继承变量，且库本身也不可被继承。

Solidity 还提供了**事件**的概念。

#### 3.4.12 事件

事件是一种可被继承的合约组件。当事件被触发时，提供给它的参数会被记录到交易日志中。这些日志保存在区块链上，在合约被添加到区块链之前，可以通过合约地址访问它们。合约内部无法访问已生成的事件，即使是产生并触发该事件的合约本身也不行。

关键字 `event` 是 Solidity 用于定义事件的语法。当事件被调用时，其参数会在事件完成后被添加到区块链上。要开始使用事件，您首先需要按如下方式声明它们：

```solidity
event moneyTransferred(address _from, address _to, uint _amount);
```

然后，您需要确保事件从函数内部发送出去：

```solidity
emit moneyTransferred(msg.sender, _to, _amount);
```

#### 3.4.13 Solidity 中的错误处理

Solidity 有多种可用于处理错误的程序。大多数情况下，当错误发生时，状态会重置为问题出现前的状态。此外还会执行额外检查以防止对代码的非法访问。

Solidity 中有几种处理错误的方式，例如 `require`、`revert` 和 `assert`。我们将留给用户自行探索这三种错误处理机制。

#### 3.4.14 Solidity 与地址

在本节中，我们将讨论如何在 Solidity 编程语言中使用和引用以太坊地址。由于整个以太坊生态系统都基于与以太币相关的交易，因此了解不同地址在 Solidity 环境中的工作方式至关重要。

以太坊生态系统中有两种账户类型：
1. **外部账户 (EOA)** – 私钥允许对账户中的任何以太币进行私有控制，以及在使用智能合约时账户所需的任何认证。私钥是生成数字签名所需的关键数据，而数字签名是签署文件以便花费账户中资金所必需的。
2. **合约账户** – 与 EOA 不同，智能合约没有关联的公钥或私钥。智能合约由其底层代码而非私钥支持。正如俗话所说，它们“拥有自身”。

每个合约的地址是从创建该合约的交易中获得的，它是发起交易的账户和 nonce（我们稍后会介绍）的函数。合约的以太坊地址可以在交易中以多种方式使用，包括作为接收方，用于向合约付款，或用于调用合约支持的某个函数。

##### 3.4.14.1 以太坊地址

哈希函数是地址创建过程中的重要组成部分。为了生成这些地址，以太坊在生成过程中使用了 **Keccak-256** 哈希函数。


