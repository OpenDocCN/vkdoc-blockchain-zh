# 第 6 章 服务层

```
19 public class BlockchainData {

21 private ObservableList<Transaction> newBlockTransactionsFX; 22 private ObservableList<Transaction> newBlockTransactions; 23 private LinkedList<Block> currentBlockChain =
    new LinkedList<>();

24 private Block latestBlock;
25 private boolean exit = false;
26 private int miningPoints;
27 private static final int TIMEOUT_INTERVAL = 65;
28 private static final int MINING_INTERVAL = 60;
29 //helper class.
30 private Signature signing = Signature
    .getInstance("SHA256withDSA");

32 //singleton class
33 private static BlockchainData instance;
```

在第 21 行，我们有一个包含`Transaction`对象的`ObservableList`。我们将仅在前端显示其内容时使用此列表。接下来，在第 22 行，我们也有一个包含`Transaction`对象的`ObservableList`。此列表将代表我们区块链的当前账本。所有关于当前账本的操作都将在此列表上执行。我们之前的列表将始终是此列表的精确副本。在第 23 行，我们终于有了当前的区块链，它被表示为一个包含`Block`对象的`LinkedList`。第 24 行将代表我们试图添加到区块链的最新区块。在第 25 行，我们有一个退出布尔值，用于辅助前端的退出命令。接下来，在第 26 行，我们有`miningPoints`整数字段，它将帮助我们跟踪共识算法中使用的挖矿积分。在第 27 和 28 行，我们有挖矿和区块链超时间隔常量。在第 30 行，我们有一个`Signature`辅助类，用于创建签名。在第 33 行，我们有作为静态对象的`BlockchainData`，这有助于我们使其成为单例类，就像`WalletData`类一样。

这只是一个关于类字段的快速总结；在我们遍历本类剩余部分时，您将更详细地看到每个字段的使用方式。

在下一个代码片段中，您将看到静态初始化器、构造函数和`getInstance()`方法的其余部分，我们将使用这些方法来创建和访问这个单例类：

```
35 static {
36     try {
37         instance = new BlockchainData();
38     } catch (NoSuchAlgorithmException e) {
39         e.printStackTrace();
40     }
41 }

43 public BlockchainData() throws NoSuchAlgorithmException {
44     newBlockTransactions = FXCollections
        .observableArrayList();
45     newBlockTransactionsFX = FXCollections
        .observableArrayList();
46 }

48 public static BlockchainData getInstance() {
49     return instance;
50 }
```