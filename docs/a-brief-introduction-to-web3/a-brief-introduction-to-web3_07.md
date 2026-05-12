# 用于获取区块链相关数据的特殊变量

以下是 Solidity 中所有全局变量及其作用的列表：

1. `blockhash(uint blockNumber)` 的返回值是：区块哈希表示；仅对最近 256 个区块有效；当前区块被忽略。
2. Coinbase 的支付地址是 `block.coinbase`。显示当前区块的地址。
3. 区块的难度由 `block.difficulty`（uint 类型）表示。
4. 函数 `block.gaslimit` 的 uint 值。显示当前区块的 gas 限制。
5. `block.number` 的 uint 值。此处显示当前区块编号。
6. 作为通用标识符的当前时间（uint 类型）：当前区块的时间戳以自 Unix 时间以来的秒数显示。
7. `msg.sig` – 你可以通过此方法获取函数标识或调用数据的前四个字节。
8. `msg.data`（调用数据的字节数组）
9. 如果你想知道当前区块的时间戳，可以使用 `now` 函数。

##### 3.4.2.1 变量命名

在 Solidity 中命名变量时，应牢记以下准则：

1. Solidity 的保留关键字不应作为变量名使用。下一部分会讨论这些关键术语。例如，像 `break` 或 `boolean` 这样的变量名是无效的。
2. Solidity 中的变量名不应以数字（0–9）开头。它们只能以字母或下划线开头。例如，变量名 `543coin` 无效，而 `_543coin` 是可接受的。
3. 在命名 Solidity 变量时，大小写很重要。`Blog` 和 `blog` 将被视为两个不同的变量。

##### 3.4.2.2 变量的作用域

局部变量是函数内部的局部变量，意味着对其状态的任何影响都仅限于该方法的范围内；而状态变量可以产生三种不同的影响：

- **公有（Public）** – 公有的状态变量既可以在内部访问，也可以通过消息访问。对于公有可访问的状态变量，会自动生成一个 getter 函数。
- **内部（Internal）** – 这些状态变量只能在同一合约内部或从该合约的子合约内部访问。
- **私有（Private）** – 这些变量的作用域非常受限——仅限于同一合约——即使从子合约也无法访问。

#### 3.4.3 值类型

你可以通过仔细阅读任何相关教程来了解 Solidity 区块链编程语言中的各种数据类型。Solidity 支持多种预定义和自定义数据类型。以下是 Solidity 中一些最常见的数据类型：

- 整数，包括有符号和无符号
- 布尔值
- 定点数，包括有符号和无符号，具有多种尺寸
- 定点数 `X x Y`，其中 *X* 定义类型所占的位数，*Y* 表示小数位数
- 无符号定点数 `X x Y`，其中 *Y* 表示无符号定点数。

#### 3.4.4 地址

以太坊地址在 Solidity 教程中由一个 20 字节的值表示，称为“地址”。要获取余额，可以使用 `.balance` 方法配合地址使用。可以使用 `.transfer` 函数将余额转移到另一个地址。

## 3.4.5 Solidity 中的运算符

与其他编程语言一样，Solidity 支持运算符。我们将在此处说明几个这样的运算符。

##### 3.4.5.1 算术运算符

算术运算包括以下内容：

1. 加法
2. 减法
3. 乘法
4. 除法
5. 递增（`++`）
6. 递减（`--`）
7. 取模（`%`）

##### 3.4.5.2 比较运算符

比较运算符包括以下内容：

1. 等于（`==`） – 检查两个操作数是否相等
2. 不等于（`!=`） – 检查两个操作数是否不相等
3. 大于（`>`） – 检查运算符左侧的操作数是否大于右侧的操作数
4. 大于或等于（`>=`） – 检查运算符左侧的操作数是否大于或等于右侧的操作数



5. 小于（`<`）—— 用于检查运算符左侧的操作数是否小于右侧的操作数  
6. 小于或等于（`<=`）—— 用于检查运算符左侧的操作数是否小于或等于右侧的操作数  

##### 3.4.5.3 逻辑运算符

Solidity 中的逻辑运算符包括以下内容：  
1. 逻辑与（`&&`）—— 如果运算符左侧的操作数为真，则返回 `true`。  
2. 逻辑或（`||`）—— 如果其中一个操作数为真，则返回 `true`。  
3. 逻辑非（`!`）—— 它反转操作数的逻辑状态，并返回操作数状态的反值。

##### 3.4.5.4 赋值运算符

Solidity 中的赋值运算符包括以下内容：  
1. 简单赋值（`a = b + c`）  
2. 加后赋值（`a += b` 等价于 `a = a + b`）  
3. 减后赋值（`a -= b` 表示 `a = a - b`）  
4. 乘后赋值（`a *= b` 表示 `a = a * b`）  
5. 除后赋值（`a /= b` 表示 `a = a / b`）  
6. 取模后赋值（`a %= b` 表示 `a = a % b`）

#### 3.4.6 循环

循环是一种简单的结构，允许在代码中重复执行某段操作。例如，假设我们要显示博客网页的点击次数。我们可以使用循环遍历博客网页的点击次数并显示结果。

与其它编程语言一样，Solidity 支持不同的循环语法。

以下是 Solidity 中 `for` 循环的示例：

```solidity
pragma solidity ⁰.8.13;

contract News {
    uint newsCount;

    constructor() public {
        newsCount = 0;
    }

    function count() public returns (uint val) {
        // for 循环示例
        for (uint i = 0; i < 5; i++) {
            newsCount++;
        }
        return newsCount;
    }
}
```

Solidity 中的 `while` 循环：

```solidity
pragma solidity ⁰.8.13;

contract News {
    uint newsCount;

    constructor() public {
        newsCount = 0;
    }

    function count() public returns (uint val) {
        uint i;
        while (i <= 5) {
            newsCount++;
            i++;
        }
        return newsCount;
    }
}
```

Solidity 中的 `do while` 循环：

```solidity
pragma solidity ⁰.8.13;

contract News {
    uint newsCount;

    constructor() public {
        newsCount = 0;
    }

    function count() public returns (uint val) {
        uint i;
        do {
            newsCount++;
            i++;
        }
        while (i <= 5);
        return newsCount;
    }
}
```

#### 3.4.7 决策流

所有编程语言（包括 Solidity）都提供在代码中应用条件逻辑的方法。基于某些条件，我们可能会选择执行某条代码路径，而当其它条件为真时则选择另一条路径。这就是决策流所实现的功能。

Solidity 支持 `if` 和 `if else` 语句，以在智能合约中实现决策流和条件逻辑。

以下是 `if` 语句的示例：

```solidity
pragma solidity ⁰.8.13;

contract News {
    uint newsCount;

    constructor() public {
        newsCount = 0;
    }

    function isFake() public returns (string memory) {
        // 这里检查 if 条件
        if (newsCount > 3)
            return "too many news articles";
        return "not much news";
    }

    function count() public returns (uint val) {
        uint i;
        do {
            newsCount++;
            i++;
        }
        while (i <= 5);
        return newsCount;
    }
}
```

类似地，我们还有用于决策的 `if else` 流程。例如：

```solidity
pragma solidity ⁰.8.13;

contract News {
    uint newsCount;

    constructor() public {
        newsCount = 0;
    }

    function isFake() public returns (string memory) {
        // 在这里检查 if else 条件
        if (newsCount > 3) {
            return "too many news articles";
        } else {
            return "not much news";
        }
    }

    function count() public returns (uint val) {
        uint i;
        do {
            newsCount++;
            i++;
        }
        while (i <= 5);
        return newsCount;
    }
}
```

#### 3.4.8 Solidity 中的函数

Solidity 通过在智能合约中创建函数来提倡代码的可重用性和可读性。函数允许我们编写可读的代码，同时也可以将特定功能定义为一个代码块。这意味着你不必重复编写相同的代码。它有助于程序员编写易于修改的代码。

函数让程序员能够将大型程序分解为更小、更易于处理的片段。

与任何其它高级编程语言一样，Solidity 拥有编写模块化函数代码所需的所有特性。

## 第 3 章 Solidity



让我们来看一个智能合约中计数函数的例子。

我们并未展示完整的合约，仅给出函数定义：

```solidity
function count() public
returns (uint val) {
  uint i;
  do {
    newsCount++;
    i++;
  }
  while (i <= 5);
  return newsCount;
}
```

这里，函数名是`count`，且不接收任何参数。它返回一个整型值，作用域为`public`。

我们也可以定义接收输入值作为参数的函数。

以下是带参数的函数示例：

```solidity
function oddEven(uint x) public returns (string memory)
{
  if (x % 2 == 0) return "even";
  else
  {
    return "odd";
  }
}
```

这里我们看到函数`oddEven`接收一个整型参数，并返回一个字符串，表示该数字是奇数还是偶数。

在 Solidity 中，函数可以有`return`语句，但并非必须。如果你希望函数返回值，则需要使用`return`。函数中的最后一条语句应该是`return`。

