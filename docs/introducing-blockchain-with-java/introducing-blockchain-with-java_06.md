# 第 2 章 模型：区块链核心

既然我们已经从理论上解释了什么是区块链以及它的用途，下一步自然是用 Java 来实现它。在本章中，我们将从创建代表应用程序最基本构建模块的模型类开始。你可以查看提供的代码片段，或者从[`github.com/5pir3x/e-coin`](https://github.com/5pir3x/e-coin)下载完整代码库。包含的练习将提供深入见解，并为你修改我的代码以创建应用程序各个方面的替代实现提供思路。我强烈建议你尽可能多地完成这些练习；希望最终你不仅能对区块链技术有更深入的理解，还能为自己的作品集增添一个出色的项目——一个不同于我的实现方案的替代方案，而不仅仅是简单的复制。我选择在代码库的`src.com.company`文件夹结构内创建一个名为`Model`的文件夹，并将模型类保存在其中。建议你为项目选择相同的文件夹结构，以避免出现路径或导入问题。

## 2.1 Block.java

我们首先列出以下代码片段中的导入语句：

```
1 package com.company.Model;
3 import sun.security.provider.DSAPublicKeyImpl;
4
5 import java.io.Serializable;
6 import java.security.InvalidKeyException;
7 import java.security.Signature;
8 import java.security.SignatureException;
9 import java.util.ArrayList;
10 import java.util.Arrays;
11 import java.util.LinkedList;
```

接下来我们进入类声明和字段部分：

```
13 public class Block implements Serializable {
15     private byte[] prevHash;
16     private byte[] currHash;
17     private String timeStamp;
18     private byte[] minedBy;
19     private Integer ledgerId = 1;
20     private Integer miningPoints = 0;
21     private Double luck = 0.0;
23     private ArrayList<Transaction> transactionLedger = new ArrayList<>();
```

第 13 行首先注意到的是，这个类实现了`Serializable`接口。由于区块链的所有区块都将使用此类创建，我们需要它们可序列化，以便通过网络共享区块链。

`prevHash`字段将包含前一区块的签名，或者说加密数据。`currHash`字段将包含当前区块的签名，或者说加密数据，该数据将使用挖到此区块的矿工的私钥进行加密。`timeStamp`字段显然包含该区块被挖出/最终确定的时间戳。`minedBy`字段将包含公钥，该公钥同时也作为成功挖到此区块的矿工的公钥地址。在区块链验证过程中，这个公钥地址/公钥将用于验证两件事：一是该区块的`currHash`/签名是否与当前区块所呈现数据的哈希值一致；二是该区块是否确实由该特定矿工挖掘。

我们将在本节稍后解释该类的`isVerified`方法时再讨论这个话题。接下来的`ledgerId`字段：由于我们打算实现一个包含独立 Block 和 Transaction 表的数据库，该字段将帮助我们检索与该区块对应的正确账本。你也可以将此字段视为区块编号。接下来的`miningPoints`和`luck`字段将用于在关于选择该区块矿工的问题上形成网络共识。

我们将在第[6](https://doi.org/10.1007/978-1-4842-7927-4_6)章详细探讨这些字段的用法。

`transactionLedger`字段只是一个包含该区块所有交易的`ArrayList`。我们将在"Transaction.java"一节中解释`Transaction`类。

在下面的代码片段中，我们可以看到从第 26 行、第 38 行和第 45 行开始的三个构造函数：

```
25 //此构造函数用于从数据库检索区块时
26 public Block(byte[] prevHash, byte[] currHash, String timeStamp, byte[] minedBy, Integer ledgerId,
27               Integer miningPoints, Double luck,
28               ArrayList<Transaction> transactionLedger) {
29     this.prevHash = prevHash;
30     this.currHash = currHash;
31     this.timeStamp = timeStamp;
32     this.minedBy = minedBy;
33     this.ledgerId = ledgerId;
34     this.transactionLedger = transactionLedger;
35     this.miningPoints = miningPoints;
36     this.luck = luck;
37 }
38 //此构造函数用于检索后初始化新区块
39 public Block(LinkedList<Block> currentBlockChain) {
40     Block lastBlock = currentBlockChain.getLast();
41     prevHash = lastBlock.getCurrHash();
42     ledgerId = lastBlock.getLedgerId() + 1;
43     luck = Math.random() * 1000000;
44 }
45 //此构造函数仅用于创建区块链中的第一个区块
46 public Block() {
47     prevHash = new byte[]{0};
48 }
```

第一个构造函数用于从数据库检索区块链时。此时我们检索到的所有区块都已完全确定，此构造函数帮助我们使用所有正确设置的字段来实例化区块。第二个构造函数在应用程序运行时使用，用于创建全新的区块（即区块链的头部）供我们处理。我们将在第[6](https://doi.org/10.1007/978-1-4842-7927-4_6)章详细讨论如何实现这一点。第 45 行的第三个构造函数仅由我们的`init()`方法使用一次，用于创建第一个区块。

下一个代码片段展示了`isVerified`方法：

```
49 public Boolean isVerified(Signature signing)
50     throws InvalidKeyException, SignatureException {
51     signing.initVerify(new DSAPublicKeyImpl(this.minedBy));
52     signing.update(this.toString().getBytes());
53     return signing.verify(this.currHash);
54 }
```

我们接受一个`Signature`类的对象作为参数。`Signature`类实际上是`java.security`包中的一个类。



`security.Signature`。它是一个辅助单例类，允许我们使用不同的算法加密/解密数据。在第 51 行，我们通过使用此类中`minedBy`字段的公钥来初始化签名参数。我们将使用此密钥来验证此类中的数据是否与`currHash`中存储的签名匹配。在第 52 行，我们插入要验证的数据，在本例中就是`toString()`方法的内容。

在第 53 行，在验证了此类中包含的数据与其`currHash`之后，我们返回布尔结果。

如下一个片段所示，剩下的内容是`equals()`和`hashCode()`方法、通用的 getter 和 setter 方法以及`toString()`方法，这些内容总结了我们的`Block.java`类：

```java
56 @Override
57 public boolean equals(Object o) {
58     if (this == o) return true;
59     if (!(o instanceof Block)) return false;
60     Block block = (Block) o;
61     return Arrays.equals(getPrevHash(), block.getPrevHash());
62 }
64 @Override
65 public int hashCode() {
66     return Arrays.hashCode(getPrevHash());
67 }
69 public byte[] getPrevHash() { return prevHash; }
70 public byte[] getCurrHash() { return currHash; }
72 public void setPrevHash(byte[] prevHash) { this.prevHash = prevHash; }
73 public void setCurrHash(byte[] currHash) { this.currHash = currHash; }
75 public ArrayList<Transaction> getTransactionLedger() {
76     return transactionLedger;
77 }
78 public void setTransactionLedger(ArrayList<Transaction> transactionLedger) {
79     this.transactionLedger = transactionLedger;
80 }
81 public String getTimeStamp() { return timeStamp; }
82 public void setMinedBy(byte[] minedBy) { this.minedBy = minedBy; }
83 public void setTimeStamp(String timeStamp) { this.timeStamp = timeStamp; }
85 public byte[] getMinedBy() { return minedBy; }
87 public Integer getMiningPoints() { return miningPoints; }
88 public void setMiningPoints(Integer miningPoints) { this.miningPoints = miningPoints; }
89 public Double getLuck() { return luck; }
90 public void setLuck(Double luck) { this.luck = luck; }
92 public Integer getLedgerId() { return ledgerId; }
93 public void setLedgerId(Integer ledgerId) { this.ledgerId = ledgerId; }
95 @Override
96 public String toString() {
97     return "Block{" +
98         "prevHash=" + Arrays.toString(prevHash) +
99         ", timeStamp='" + timeStamp + '\'' +
100         ", minedBy=" + Arrays.toString(minedBy) +
101         ", ledgerId=" + ledgerId +
102         ", miningPoints=" + miningPoints +
103         ", luck=" + luck +
104         '}';
105 }
106 }
```

首先要注意的是，`equals()`方法比较了区块类的上一个哈希值。我们将在第 6 章进一步解释共识算法时使用它。另一个需要注意的是`toString()`方法中包含的字段。我们包含了用于验证区块是否与当前哈希匹配的所有内容。

**重要提示！**

*   请记住，钱包的公钥也是钱包的公共地址/账户号。
*   区块的当前哈希/签名只是区块中包含数据的加密版本。
*   矿工的私钥用于加密区块数据，从而创建签名。
*   矿工的公钥供其他对等节点通过将签名的哈希值与区块数据的哈希值进行比较来验证区块。
*   请注意我们如何在整个类中使用`toString()`方法来方便地准备数据以进行比较。
*   请注意，确保区块唯一性的所有必要字段都包含在`toString()`方法中。

## 2.2 Transaction.java



