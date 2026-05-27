# 排版后内容

`Lines 30–34`是用于建立数据库连接的常规 JDBC 代码，我们在此数据库中保存钱包密钥，并执行`select`查询以检索它们。在第 35 行，我们初始化了`KeyFactory`辅助类。它来自`java.security`包，将帮助我们创建`PublicKey`和`PrivateKey`对象。在第 36 和 37 行，我们声明了`PublicKey`和`PrivateKey`对象。在第 39 和 40 行，我们使用`KeyFactory`辅助类通过插入从数据库检索到的字节数组来生成公钥。然而，由于`generatePublic(KeySpec ks)`接受的是`KeySpec`对象而非字节数组，我们必须通过`X509EncodedKeySpec`构造函数来创建`KeySpec`对象。该类的父类实现了`KeySpec`接口，这意味着我们可以用它来生成公钥，同时该类的构造函数接受我们的字节数组，如图 6-1 所示。

图 6-1. `X509EncodedKeySpec`类

第 41 和 42 行使用类似的方法和`PKCS8EncodedKeySpec`类为我们生成私钥。它也接受一个字节数组，并构建一个实现`KeySpec`接口的对象，如图 6-2 所示。

图 6-2. `PKCS8EncodedKeySpec`类

这两个类都来自`java.security`包，这对我们来说是一个巨大的资产，因为我们可以直接使用它们。最后，在第 44 行，我们使用从数据库检索到的公钥和私钥创建了一个新的钱包对象，并将其赋值给类中的`wallet`字段。