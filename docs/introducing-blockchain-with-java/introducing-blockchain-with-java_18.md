# 第 4 章 构建用户界面

### 4.3.2 AddNewTransactionController

让我们看一下 `AddNewTransactionController.fxml` 中第二个控制器的代码，如下面的代码片段所示：

```
1 package com.company.Controller;

3 import com.company.Model.Transaction;
4 import com.company.ServiceData.BlockchainData;
5 import com.company.ServiceData.WalletData;
6 import javafx.fxml.FXML;
7 import javafx.scene.control.TextField;

9 import java.security.GeneralSecurityException;
10 import java.security.Signature;
11 import java.util.Base64;

13 public class AddNewTransactionController {

15 @FXML
16 private TextField toAddress;
17 @FXML
18 private TextField value;

20 @FXML
21 public void createNewTransaction()
22 throws GeneralSecurityException {
23 Base64.Decoder decoder = Base64.getDecoder();
24 Signature signing =
25 Signature.getInstance("SHA256withDSA");
26 Integer ledgerId = BlockchainData.getInstance()
27 .getTransactionLedgerFX().get(0).getLedgerId();
28 byte[] sendB = decoder.decode(toAddress.getText());
29 Transaction transaction =
30 new Transaction(WalletData.getInstance()
31 .getWallet(), sendB, Integer.parseInt(
32 value.getText()), ledgerId, signing);
33 BlockchainData.getInstance()
34 .addTransaction(transaction,false);
35 BlockchainData.getInstance()
36 .addTransactionState(transaction);
37 }
38 }
```

如你所见，它遵循了类似的设计模式。它只包含视图中引用的字段和方法，并适当地为它们添加了注解。我们来解释一下 `createNewTransaction()` 方法中的代码。第 22 和 23 行从我们的辅助类中实例化了对象。

第 24 行在点击按钮时获取当前的 `ledgerId`。

在第 25 行，我们使用 `Base64` 对象将地址从字符串转换回字节数组，以便第 26 和 27 行可以用它来创建新交易。在第 28 行，我们调用服务层将此交易添加到数据库的当前区块中。在第 29 行，我们调用服务层来调整当前应用状态，以包含此交易。

