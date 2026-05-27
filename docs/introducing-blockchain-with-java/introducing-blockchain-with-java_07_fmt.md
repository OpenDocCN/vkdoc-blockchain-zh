# 排版后的文档

在上一节中，我们简要提到在`Block`类中维护了一个交易数组列表，现在详细解释`Transaction.java`类的内容。首先从以下代码片段中的导入开始：

```java
1 package com.company.Model;
3 import sun.security.provider.DSAPublicKeyImpl;
5 import java.io.Serializable;
6 import java.security.InvalidKeyException;
7 import java.security.Signature;
8 import java.security.SignatureException;
9 import java.time.LocalDateTime;
10 import java.util.Arrays;
11 import java.util.Base64;
```