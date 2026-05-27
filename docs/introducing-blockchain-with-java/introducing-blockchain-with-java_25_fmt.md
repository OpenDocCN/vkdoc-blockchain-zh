# 第 6 章 服务层

本类中的最后一个方法是`getWallet()`方法，如第 47 行所示。它是一个简单的 getter 方法，用于将钱包对象提供给应用程序的其他部分。

## 6.2 BlockchainData

这是应用程序中最大、最复杂的类。在这里，我们可以找到与区块链相关的所有功能。让我们先展示下一个代码片段中的所有导入，以供参考：

```
1 package com.company.ServiceData;

3 import com.company.Model.Block;

4 import com.company.Model.Transaction;

5 import com.company.Model.allet;

6 import javafx.collections.FXCollections;

7 import javafx.collections.ObservableList;

8 import sun.security.provider.DSAPublicKeyImpl; 9

10 import java.security.*;

11 import java.sql.*;

12 import java.time.LocalDateTime;

13 import java.time.ZoneOffset;

14 import java.util.ArrayList;

15 import java.util.Arrays;

16 import java.util.Comparator;

17 import java.util.LinkedList;
```

接下来，我们来看一下该类中使用的字段，如下一个代码片段所示：