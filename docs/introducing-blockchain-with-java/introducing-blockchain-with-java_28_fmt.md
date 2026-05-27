# 排版后内容

```
79 if (Arrays.equals(transaction.getFrom(), walletAddress.getEncoded())) {
80 balance -= transaction.getValue();
81 }
82 }
83 return balance;
84 }
```

我们先从第 65 行的`getBalance`方法开始，因为它被第 60 行的方法所调用。该方法会遍历给定的区块链以及包含新区块待定交易的当前账本，然后找出指定公钥地址的余额。此方法不仅可用于显示当前余额，还能在发送代币前验证某个地址是否拥有足够的余额。第 65 和 66 行显示了我们刚才提到的输入参数。在第 68 至 77 行，我们遍历区块链中的每个区块，并对每笔交易检查指定地址是发送方还是接收方，同时相应调整余额。

为了防止双重支付，我们还需要减去当前交易账本中正在尝试发送的任何资金。

请记住，双重支付是指发送方试图多次发送其全部资金，从而凭空倍增资金的情况。这种情况发生在第 78–82 行。如果你想知道为什么我们要在交易被写入区块链后才统计入账资金，这是为了防止因先入账无效交易而导致共识机制发现并作废该交易前，我们就能将入账资金转出的漏洞。最后，在第 83 行，我们返回计算出的余额。

现在回到第一个方法（第 60–63 行），可以看到它简单地调用了`getBalance`方法，并传入本地区块链、当前账本以及我们钱包的公钥地址。当整个应用程序中需要查询当前余额时，就会调用此方法。

接下来，让我们看看下一段代码片段中的方法：

```
86 private void verifyBlockChain(LinkedList<Block> currentBlockChain) throws
GeneralSecurityException {
87 for (Block block : currentBlockChain) {
88 if (! block.isVerified(signing)) {
89 throw new GeneralSecurityException(
"Block validation failed" );
90 }
91 ArrayList<Transaction> transactions = block
.getTransactionLedger();
92 for (Transaction transaction : transactions) {
93 if (! transaction.isVerified(signing)) {
94 throw new GeneralSecurityException(
"Transaction validation failed" );
95 }
96 }
97 }
98 }
```

这是实际检查每笔交易和每个区块有效性的方法。在此方法中，我们终于可以看到`Block`和`Transaction`类中的`isVerified`方法如何实际运行，以及它们如何融入我们的应用程序。如果需要，可以回顾第 2 章来回忆这些方法的工作原理。该方法本身很简单：它遍历所有区块以及每个区块内的所有交易，并逐一验证其有效性。在本书后续内容中，你将看到此方法不仅用于验证我们自己的区块链，还用于验证从其他节点接收到的区块链。

现在，让我们转到下一段代码片段：

```
99 public void addTransactionState(Transaction transaction) {
100 newBlockTransactions.add(transaction);
101 newBlockTransactions.sort(transactionComparator);
}
```

```
104 public void addTransaction(Transaction transaction, boolean blockReward) throws GeneralSecurityException {
105 try {
106 if (getBalance(currentBlockChain, newBlockTransactions,
107 new DSAPublicKeyImpl(transaction.getFrom())
108 ) < transaction.getValue() && ! blockReward) {
109 throw new GeneralSecurityException("Not enough
110 funds by sender to record transaction" );
111 } else {
112 Connection connection = DriverManager.getConnection("jdbc:sqlite:C:\\Users\\spiro\\IdeaProjects\\e-coin\\db\\blockchain.db");
113 PreparedStatement pstmt;
114 pstmt = connection.prepareStatement("INSERT INTO TRANSACTIONS " +
115 "(\"FROM\", \"TO\", LEDGER_ID, VALUE, SIGNATURE, CREATED_ON) " +
116 " VALUES (?,?,?,?,?,?) ");
117 pstmt.setBytes(1, transaction.getFrom());
118 pstmt.setBytes(2, transaction.getTo());
119 pstmt.setInt(3, transaction.getLedgerId());
120 pstmt.setInt(4, transaction.getValue());
121 pstmt.setBytes(5, transaction.getSignature());
122 pstmt.setString(6, transaction.getTimestamp());
123 pstmt.executeUpdate();
125 pstmt.close();
126 connection.close();
127 }
128 } catch (SQLException e) {
129 System.out.println("数据库问题：" + e.getMessage());
130 e.printStackTrace();
131 }
133 }
```

![](img/index-132_1.jpg)