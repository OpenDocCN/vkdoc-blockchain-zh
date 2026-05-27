# 第 2 章 模型：区块链核心

接下来查看类声明及其字段，如下一个代码片段所示：

```java
13 public class Transaction implements Serializable {
15 private byte[] from;
16 private String fromFX;
17 private byte[] to;
18 private String toFX;
19 private Integer value;
20 private String timestamp;
21 private byte[] signature;
22 private String signatureFX;
23 private Integer ledgerId;
```

由于此类也会创建构成区块链的对象，它将实现`Serializable`接口，以便通过网络共享。字段`from`和`to`分别包含发送方和接收方账户的公钥/地址。`value`是发送的货币数量，`timestamp`是交易发生的时间。`signature`包含所有字段的加密信息，用于验证交易的有效性（其使用方式与上一类中`currHash`字段相同）。`ledgerId`的作用与上一类中相同。带有`FX`后缀的字段是简单的副本，格式化为`String`而非`byte[]`。这样做的目的是为了方便在前端显示。

在本类中还有两个构造函数：第一个用于从数据库检索交易时使用，第二个用于在应用程序内创建新交易时使用。让我们在以下代码片段中观察它们：

```java
26 // 用于加载现有签名的构造函数
27 public Transaction(byte[] from, byte[] to, Integer value, byte[] signature, Integer ledgerId,
28                    String timeStamp) {
29     Base64.Encoder encoder = Base64.getEncoder();
30     this.from = from;
31     this.fromFX = encoder.encodeToString(from);
32     this.to = to;
33     this.toFX = encoder.encodeToString(to);
34     this.value = value;
35     this.signature = signature;
36     this.signatureFX = encoder.encodeToString(signature);
37     this.ledgerId = ledgerId;
38     this.timestamp = timeStamp;
39 }
40 // 用于创建新交易并对其签名的构造函数
41 public Transaction(Wallet fromWallet, byte[] toAddress, Integer value, Integer ledgerId,
42                    Signature signing) throws InvalidKeyException, SignatureException {
43     Base64.Encoder encoder = Base64.getEncoder();
44     this.from = fromWallet.getPublicKey().getEncoded();
45     this.fromFX = encoder.encodeToString(fromWallet.getPublicKey().getEncoded());
46     this.to = toAddress;
47     this.toFX = encoder.encodeToString(toAddress);
48     this.value = value;
49     this.ledgerId = ledgerId;
50     this.timestamp = LocalDateTime.now().toString();
51     signing.initSign(fromWallet.getPrivateKey());
52     String sr = this.toString();
53     signing.update(sr.getBytes());
54     this.signature = signing.sign();
55     this.signatureFX = encoder.encodeToString(this.signature);
56 }
```