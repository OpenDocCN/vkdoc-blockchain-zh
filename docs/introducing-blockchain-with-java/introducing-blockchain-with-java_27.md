# 第 6 章 服务层

如果您还记得，我们在上一节讨论`WalletData`类时已经解释了这些元素的功能。让我们来看下一个代码片段：

```
52 Comparator<Transaction> transactionComparator = Comparator
    .comparing(Transaction::getTimestamp);
53 public ObservableList<Transaction> getTransactionLedgerFX() {
54     newBlockTransactionsFX.clear();
55     newBlockTransactions.sort(transactionComparator);
56     newBlockTransactionsFX.addAll(newBlockTransactions);
57     return FXCollections.observableArrayList(
        newBlockTransactionsFX);
58 }
```

在第 53 到 58 行，我们有一个简单的方法，用于显示当前交易账本。它只是将交易从当前账本传输到一个可以在 UI 上显示的`ObservableList`中。我们这样做仅仅是为了保持关注点分离。唯一需要提及的其他事情是第 52 行的`transactionComparator`，它在创建时对我们的交易进行排序。

让我们继续下一个代码片段：

```
60 public String getWalletBallanceFX() {
61     return getBalance(currentBlockChain, newBlockTransactions,
62         WalletData.getInstance().getWallet()
63             .getPublicKey()).toString();
64 }

65 private Integer getBalance(LinkedList<Block> blockChain,
66     ObservableList<Transaction> currentLedger, PublicKey walletAddress) {
67     Integer balance = 0;
68     for (Block block : blockChain) {
69         for (Transaction transaction : block
70             .getTransactionLedger()) {
71             if (Arrays.equals(transaction
72                 .getFrom(), walletAddress.getEncoded())) {
73                 balance -= transaction.getValue();
74             }
75             if (Arrays.equals(transaction
76                 .getTo(), walletAddress.getEncoded())) {
77                 balance += transaction.getValue();
78             }
79         }
80     }
81     for (Transaction transaction : currentLedger) {
```



