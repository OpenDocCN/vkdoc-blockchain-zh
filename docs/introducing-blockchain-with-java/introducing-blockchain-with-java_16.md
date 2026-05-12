# 第 4 章 构建用户界面

您还记得在 `MainWindow.fxml` 部分，我们如何为控制器类中的字段添加了 `fx:id` 引用吗？

现在我们来创建这些字段。所有这些字段的类都来自 `javafx` 包，该包已包含在您的 Java SDK 中。我们需要使用 `@FXML` 标签注释每个元素，并将每个字段命名为与 `fx:id` 引用相同的名称。如果操作正确，行号旁边会出现一个图标，点击它会直接跳转到 FXML 文件中引用该字段的确切位置。同时要确保字段的类与我们选择的 FXML 文件中的元素类型相对应。

接下来，我们来看下面的代码片段，将讨论控制器中的 `initialize()` 方法：

```java
34 private TextField eCoins;
35 @FXML
36 private TextArea publicKey;
38 public void initialize() {
39     Base64.Encoder encoder = Base64.getEncoder();
40     from.setCellValueFactory(new PropertyValueFactory<>("fromFX"));
41     to.setCellValueFactory(new PropertyValueFactory<>("toFX"));
42     value.setCellValueFactory(new PropertyValueFactory<>("value"));
43     signature.setCellValueFactory(new PropertyValueFactory<>("signatureFX"));
44     timestamp.setCellValueFactory(new PropertyValueFactory<>("timestamp"));
45     eCoins.setText(BlockchainData.getInstance().getWalletBallanceFX());
46     publicKey.setText(encoder.encodeToString(
47         WalletData.getInstance().getWallet().getPublicKey().getEncoded()));
48     tableview.setItems(BlockchainData.getInstance().getTransactionLedgerFX());
49     tableview.getSelectionModel().select(0);
50 }
```

在第 39 行，我们初始化了 `Base64.Encoder` 编码器。在第 40–49 行，我们为表格列创建了单元格值工厂。换句话说，我们引用了表格视图所包含的类中的字段值，以便在相应的列中显示。在第 50 行，我们设置了要显示的硬币余额。这里您会注意到我们使用了服务层的方法来实现这一点，我们将在下一章中详细解释。第 51 行使用 `Base64.Encoder` 来编码我们的公钥，通过服务层类将其作为字节数组检索，并转换为字符串进行显示。在第 52 行，我们通过服务层方法 `BlockchainData.getInstance().getTransactionLedgerFX()` 检索要显示的交易，然后将其设置到 `tableview` 中进行显示。最后，第 53 行默认选中第一条交易。

接下来，我们来看最后一个代码片段，并解释剩余的方法：

```java
56 @FXML
57 public void toNewTransactionController() {
58     Dialog<ButtonType> newTransactionController = new Dialog<>();
59     newTransactionController.initOwner(borderPane.getScene().getWindow());
60     FXMLLoader fxmlLoader = new FXMLLoader();
61     fxmlLoader.setLocation(getClass().getResource("../View/AddNewTransactionWindow.fxml"));
62     try {
63         newTransactionController.getDialogPane().setContent(fxmlLoader.load());
64     } catch (IOException e) {
65         System.out.println("Cant load dialog");
66         e.printStackTrace();
67         return;
68     }
69     newTransactionController.getDialogPane().getButtonTypes().add(ButtonType.FINISH);
70     Optional<ButtonType> result = newTransactionController.showAndWait();
71     if (result.isPresent()) {
72         tableview.setItems(BlockchainData.getInstance().getTransactionLedgerFX());
73         eCoins.setText(BlockchainData.getInstance().getWalletBallanceFX());
74     }
75 }
77 @FXML
78 public void refresh() {
79     tableview.setItems(BlockchainData.getInstance().getTransactionLedgerFX());
80 }
```



80 `tableview.getSelectionModel().select(0);`
81 `eCoins.setText(BlockchainData.getInstance().getWalletBallanceFX());`

82 `}`

84 `@FXML`
85 `public void handleExit() {`

![index-98_1.jpg](img/index-98_1.jpg)

