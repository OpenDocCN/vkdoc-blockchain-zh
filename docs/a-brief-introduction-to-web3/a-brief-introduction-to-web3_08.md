# 第 3 章 Solidity

##### 3.4.8.1 函数修饰符

函数修饰符是 Solidity 中的一个重要概念。它允许我们将代码中一些通用方面外部化到修饰符中，从而提高代码的可重用性。例如，如果希望在函数调用前后打印一条消息，一种方式是将此代码放入每个函数体中；另一种方式是创建一个修饰符，它充当函数的占位符，并允许我们在代码周围织入一些逻辑。

修饰符最常用于在函数运行前自动检查某个条件。例如，如果希望在调用函数之前验证用户身份，我们会将身份验证逻辑放入修饰符中。在函数被调用之前，这段身份验证代码会先执行。如果身份验证成功，则执行函数；否则会抛出异常。

以下是修饰符的示例：

```solidity
modifier SampleModifier {
  // 修饰符的逻辑
}
```

修饰符也可以接收输入参数，例如：

```solidity
modifier SampleModifier(string x) {
  // 修饰符的逻辑
}
```

让我们分析修饰符的工作原理。这里，我们创建了一个名为`checkOwner`的修饰符：

```solidity
modifier checkOwner {
  require(msg.sender == owner);
  _;
}
```

读者可以看到修饰符代码中有`_;`。这被称为合并通配符。可以将其视为要执行的函数的占位符。为了清晰解释，这是附加了此修饰符的函数将要执行的位置。这使我们能够在函数执行前后放置一些通用代码。熟悉面向切面编程的读者可以在这里找到相似之处。为了正常工作，修饰符必须在主体中包含`_;`符号。

这里展示了`_;`如何放置在修饰符中：

```solidity
modifier executeBefore {
  require(/* 在函数之前执行的代码 */);
  _; // 这里的 _ 表示实际要执行的函数
}
```

这个例子展示了通过修饰符进行后处理。函数执行发生在修饰符调用之前：

```solidity
modifier executeAfter {
  _; // 执行实际函数
  require(/* 在函数之后执行的代码 */);
}
```

以下是使用示例。函数`isValidUser`仅在用户有效时返回`true`。这里有两种选择：要么将用户有效性检查放入每个需要此检查的函数中，要么通过修饰符将检查外部化，同时实现清晰的关注点分离。

我们认为使用修饰符是更清晰、更好的方法。以下展示了如何通过修饰符实现这一点：

```solidity
function isValidUser(address _user) public view returns(bool) {
  // 检查 _user 是否有效的逻辑
  return true;
}

modifier CheckUser {
  require(isValidUser(msg.sender));
  _;
}
```

还有一种方法可以将多个修饰符应用于一个函数：

```solidity
contract Sample {
  modifier A() {
    require(
      // 一些检查
    );
    _;
  }
  modifier B() {
    require(
      // 更多检查
    );
    _;
  }
  function test() public B() A() {
    // 函数代码
  }
}
```



