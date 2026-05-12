# 第六章 服务层

> *“分层” — Filip Rizov*
>
> © Spiro Buzharovski 2022
>
> S. Buzharovski, *Introducing Blockchain with Java*,
>
> https://doi.org/10.1007/978-1-4842-7927-4_6

本章将介绍创建服务层，它将为前几章涵盖的所有内容提供功能。在本章中，我们最终将实现之前章节中简要提到的所有方法。这一章很可能是目前为止最难理解的一章，因为它将尝试把我们应用程序的所有独立元素整合在一起。我们建议在继续本章之前，先熟练掌握之前各章的内容。

## 6.1 `WalletData`

在本节中，我们将解释`WalletData`类。它用于在应用程序运行时存储我们的钱包，并使其可供应用程序的其他部分使用。钱包的任何额外功能都应通过此类实现。

现在让我们解释以下代码：

```java
package com.company.ServiceData;

import com.company.Model.Wallet;

import java.security.KeyFactory;
import java.security.NoSuchAlgorithmException;
import java.security.PrivateKey;
import java.security.PublicKey;
import java.security.spec.InvalidKeySpecException;
import java.security.spec.PKCS8EncodedKeySpec;
import java.security.spec.X509EncodedKeySpec;
import java.sql.*;

public class WalletData {

    private Wallet wallet;
    //singleton class
    private static WalletData instance;

    static {
        instance = new WalletData();
    }

    public static WalletData getInstance() {
        return instance;
    }
```

首先，我们进行常规的类导入。然后，由于我们将钱包存储在这个类中，我们在第 16 行有一个`Wallet`类的字段。

现在，因为我们希望同一个钱包在整个应用程序中可用，我们将把这个类创建为单例类。单例是一种基本的设计模式，意味着这个类在内存中只会作为一个单一对象存在，并且我们无法实例化任何额外的对象。这意味着我们的应用程序中不会出现钱包重复的情况。我们通过在第 18 行有一个`WalletData`类的静态字段，然后在第 20-22 行静态初始化它，使我们的类成为单例类。最后要提到的是`getInstance()`方法。我们使用此方法来访问该类，而不是尝试实例化新对象。让我们查看下一个代码片段，并解释此类中包含的其余代码：

```java
    //This will load your wallet from the database.
    public void loadWallet() throws SQLException, NoSuchAlgorithmException, InvalidKeySpecException {
        Connection walletConnection = DriverManager.getConnection(
                "jdbc:sqlite:C:\\Users\\spiro\\IdeaProjects\\e-coin\\db\\wallet.db");
        Statement walletStatment = walletConnection.createStatement();
        ResultSet resultSet;
        resultSet = walletStatment.executeQuery(" SELECT * FROM WALLET ");
        KeyFactory keyFactory = KeyFactory.getInstance("DSA");
        PublicKey pub2 = null;
        PrivateKey prv2 = null;
        while (resultSet.next()) {
            pub2 = keyFactory.generatePublic(
                    new X509EncodedKeySpec(resultSet.getBytes("PUBLIC_KEY")));
            prv2 = keyFactory.generatePrivate(
                    new PKCS8EncodedKeySpec(resultSet.getBytes("PRIVATE_KEY")));
        }
        this.wallet = new Wallet(pub2, prv2);
    }

    public Wallet getWallet() {
        return wallet;
    }
}
```

首先，我们有`loadWallet()`方法。顾名思义，我们使用它从数据库加载钱包。如果你还记得，我们在`ECoin`类的`init()`方法中使用此方法来加载钱包。



