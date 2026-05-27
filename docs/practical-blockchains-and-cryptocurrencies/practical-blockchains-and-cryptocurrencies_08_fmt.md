# 8. Helium 区块链

在本章中，我们将延续上一章的内容，开始构建`Helium`区块链。首先，我们将为`Helium`使用的加密函数创建接口，然后开发一些`Helium`的区块链功能。

如果您主要对区块链和加密货币的理论感兴趣，请随意跳转到下一个理论章节。

## Python 加密包

`Helium`大量使用了加密函数，特别是`SHA-256`、`RIPEMD-160`和数字签名。这些功能在Python的`pycryptodome`包中可用。因此，让我们安装这个包。请确保您位于`Helium`虚拟环境的根目录，并且虚拟环境已激活。执行：

```
$(virtual) pip install pycryptodome
```

我们还需要一些辅助性的Python模块。首先，我们将使用Python的`re`模块。`re`包含在标准Python发行版中，因此无需安装。它是Python的正则表达式解析器，用于检查字符串中是否存在指定的子字符串，并且还可以执行文本替换。

接下来，我们必须安装`Base58`模块。`Base58`将大字符串转换为受限的字母数字字符串。`Base58`的字母数字字符集是：

```
123456789ABCDEFGHJKLMNPQRSTUVWXYzabcdefghijkmnopqrstuvwxyz
```

一个`Base58`编码的字符串仅包含上述字符集中的字符。

`Base58`字符集与普通的字母数字字符集不同，因为它排除了容易混淆的字符。被排除的字符是`I`（大写 i）、`l`（小写 L）、`O`（大写 o）和`0`（零）。^(³⁷)

我们还将使用`pdb`调试器。`pdb`使我们能够在源代码中设置断点，逐行执行源代码，并检查堆栈帧和变量的值。^(³⁸) `pdb`是Python标准发行版的一部分，因此无需通过`pip`安装。我们将始终在`Helium`源代码文件中导入`pdb`模块。

## `rcrypt` 模块解读

以下模块文件`rcrypt.py`是`Helium`连接加密包`pycryptodome`的接口。将以下`rcrypt`代码复制到一个名为`rcrypt.py`的文件中，并将该文件保存在`crypt`子目录下。^(³⁹)

现在，我们准备开始对`rcrypt`接口模块进行代码解读：

### `rcrypt` 模块

`rcrypt`模块实现了 Helium 加密货币应用所需的多种加密函数。

### 依赖项

- 本模块需要安装`pycryptodome`包。
- `base58`包用于将字符串编码为 base58 格式。
- 本模块使用 Python 的正则表达式模块`re`。
- 本模块使用 Python 标准库中的`secrets`模块来生成加密安全的十六进制编码字符串。

### 导入

```python
#### 导入正则表达式模块
import re

#### 从 cryptodome 包中导入
from Crypto.Hash import SHA3_256
from Crypto.PublicKey import ECC
from Crypto.PublicKey import DSA
from Crypto.Signature import DSS
from Crypto.Hash import SHA256
from Crypto.Hash import RIPEMD160
import base58
import secrets

#### 导入 Python 的调试和日志模块
import pdb
import logging
```

### 日志配置

```python
"""
将调试消息记录到文件 debug.log 中
"""
logging.basicConfig(filename="debug.log", filemode="w", \
                    format='%(asctime)s:%(levelname)s:%(message)s', level=logging.DEBUG)
```

### 函数

#### `make_SHA256_hash(msg: 'string') -> 'string'`

`make_SHA256_hash`计算接收到的字符串参数的 SHA-256 消息摘要或加密哈希。生成的哈希值被转换为十六进制数字序列，然后由函数返回。消息摘要的十六进制格式长度为 64 字节。

```python
def make_SHA256_hash(msg: 'string') -> 'string':
    """
    make_sha256_hash 计算接收到的字符串参数的 SHA-256 消息摘要或加密哈希。
    生成的哈希值被转换为十六进制数字序列，然后由函数返回。
    消息摘要的十六进制格式长度为 64 字节。
    """
    # 将接收到的 msg 字符串转换为 ASCII 字节序列
    message = bytes(msg, 'ascii')
    # 计算 msg 的 SHA-256 消息摘要并转换为十六进制格式
    hash_object = SHA256.new()
    hash_object.update(message)
    return hash_object.hexdigest()
```

#### `validate_SHA256_hash(digest: "string") -> bool`

`validate_SHA256_hash`: 测试一个字符串的编码是否符合 SHA-256 消息摘要的十六进制字符串格式（64 字节）。

```python
def validate_SHA256_hash(digest: "string") -> bool:
    """
    validate_SHA256_hash: 测试一个字符串的编码是否符合
    SHA-256 消息摘要的十六进制字符串格式（64 字节）。
    """
    # 十六进制 SHA256 消息摘要必须为 64 字节长度。
    if len(digest) != 64: return False
    # 此正则表达式测试接收到的字符串是否仅包含十六进制字符
    if re.search('[⁰-9a-fA-F]', digest) == None: return True
    return False
```

#### `make_RIPEMD160_hash(message: 'byte stream') -> 'string'`

RIPEMD-160 是一种加密算法，它生成 20 字节的消息摘要。此函数计算消息的 RIPEMD-160 消息摘要，并返回消息摘要的十六进制字符串编码表示形式（40 字节）。

```python
def make_RIPEMD160_hash(message: 'byte stream') -> 'string':
    """
    RIPEMD-160 是一种加密算法，它生成 20 字节的消息摘要。
    此函数计算消息的 RIPEMD-160 消息摘要，并返回
    消息摘要的十六进制字符串编码表示形式（40 字节）。
    """
    # 将消息转换为 ASCII 字节流
    bstr = bytes(message, 'ascii')
    # 生成消息的 RIPEMD 哈希
    h = RIPEMD160.new()
    h.update(bstr)
    # 转换为十六进制编码字符串
    hash = h.hexdigest()
    return hash
```

#### `validate_RIPEMD160_hash(digest: 'string') -> 'bool'`

测试接收到的字符串的编码是否符合十六进制格式的 RIPE160 哈希。

```python
def validate_RIPEMD160_hash(digest: 'string') -> 'bool':
    """
    测试接收到的字符串的编码是否符合十六进制格式的 RIPE160 哈希
    """
    if len(digest) != 40: return False
    # 此正则表达式测试接收到的字符串是否仅包含十六进制字符
    if re.search('[⁰-9a-fA-F]+', digest) == None: return True
    return False
```

#### `make_ecc_keys()`

使用`pycryptodome`包中的椭圆曲线加密函数生成私钥-公钥对。返回包含 PEM 格式的私钥和公钥的元组。

```python
def make_ecc_keys():
    """
    使用 pycryptodome 包中的椭圆曲线加密函数生成私钥-公钥对。
    返回包含 PEM 格式的私钥和公钥的元组
    """
    # 生成椭圆曲线对象
    ecc_key = ECC.generate(curve='P-256')
    # 获取公钥对象
    pk_object = ecc_key.public_key()
    # 以 PEM 格式导出私钥-公钥对
    p = (ecc_key.export_key(format='PEM'), pk_object.export_key(format='PEM'))
    return p
```

#### `sign_message(private_key: 'String', message: 'String') -> 'string'`

使用`pycryptodome`包的椭圆曲线加密模块生成的私钥对消息进行数字签名。接收 PEM 格式的私钥和要进行数字签名的消息。返回十六进制编码的签名字符串。

```python
def sign_message(private_key: 'String', message: 'String') -> 'string':
    """
    使用 pycryptodome 包的椭圆曲线加密模块生成的私钥
    对消息进行数字签名。
    接收 PEM 格式的私钥和要进行数字签名的消息。
    返回十六进制编码的签名字符串。
    """
    # 导入 PEM 格式的私钥
    priv_key = ECC.import_key(private_key)
    # 将消息转换为字节流并计算消息的 SHA-256 消息摘要
    bstr = bytes(message, 'ascii')
    hash = SHA256.new(bstr)
    # 从私钥创建数字签名对象
    signer = DSS.new(priv_key, 'fips-186-3')
    # 对 SHA-256 消息摘要进行签名。
    signature = signer.sign(hash)
    sig = signature.hex()
    return sig
```

#### `verify_signature(public_key: 'String', msg: 'String', signature: 'string') -> 'bool'`

测试消息是否由与公钥配对的私钥进行数字签名。接收 PEM 格式的 ECC 公钥、待验证的消息以及消息的数字签名。返回`True`或`False`。

```python
def verify_signature(public_key: 'String', msg: 'String', signature: 'string') -> 'bool':
    """
    测试消息是否由与公钥配对的私钥进行数字签名。
    接收 PEM 格式的 ECC 公钥、待验证的消息
    以及消息的数字签名。
    返回 True 或 False
    """
    try:
        # 将消息转换为字节流并计算 SHA-256 哈希
        msg = bytes(msg, 'ascii')
        msg_hash = SHA256.new(msg)
        # 签名转换为字节
        signature = bytes.fromhex(signature)
        # 导入 PEM 格式的公钥并从公钥创建签名验证器对象
        pub_key = ECC.import_key(public_key)
        verifier = DSS.new(pub_key, 'fips-186-3')
        # 验证签名消息的真实性
        verifier.verify(msg_hash, signature)
        return True
    except Exception as err:
        logging.debug('verify_signature: exception: ' + str(err))
```

#### `make_address(prefix: 'string') -> 'string'`

根据 PEM 格式的 ECC 公钥生成 Helium 地址。`prefix`是描述地址类型的单个数字字符。此前缀必须为`'1'`。

```python
def make_address(prefix: 'string') -> 'string':
    """
    根据 PEM 格式的 ECC 公钥生成 Helium 地址。
    prefix 是描述地址类型的单个数字字符。
    此前缀必须为 '1'
    """
    key = ECC.generate(curve='P-256')
    __private_key = key.export_key(format='PEM')
    public_key = key.public_key().export_key(format='PEM')
    val = make_SHA256_hash(public_key)
    val = make_RIPEMD160_hash(val)
    tmp = prefix + val
    # 生成校验和
    checksum = make_SHA256_hash(tmp)
    checksum = checksum[len(checksum) - 4:]
    # 将校验和添加到 tmp 结果
    address = tmp + checksum
    # 将 addr 编码为 base58 字节序列
    address = base58.b58encode(address.encode())
    # decode 函数将字节序列转换为字符串
    address = address.decode("ascii")
    return address
```

#### `validate_address(address: 'string') -> bool`

使用附加到地址的四字符校验和验证 Helium 地址。接收 base58 编码的地址。

```python
def validate_address(address: 'string') -> bool:
    """
    使用附加到地址的四字符校验和验证 Helium 地址。
    接收 base58 编码的地址。
    """
    # 将字符串地址编码为字节序列
    addr = address.encode("ascii")
    # 反转地址的 base58 编码
    addr = base58.b58decode(addr)
    # 将地址转换为字符串
    addr = addr.decode("ascii")
    # 长度必须为 RIPEMD-160 哈希长度 + 校验和长度 + 1
    if (len(addr) != 45): return False
    if (addr[0] != '1'):  return False
    # 提取校验和
    extracted_checksum = addr[len(addr) - 4:]
    # 从 addr 中提取校验和并计算剩余 addr 字符串的 SHA-256 哈希
    tmp = addr[:len(addr)- 4]
    tmp = make_SHA256_hash(tmp)
    # 从 tmp 获取计算出的校验和
    checksum = tmp[len(tmp) - 4:]
    if extracted_checksum == checksum: return True
    return False
```

#### `make_uuid() -> 'string'`

生成一个编码为十六进制字符串的全局唯一 256 位 ID，用作交易标识符。使用 Python 标准库`secrets`模块生成加密安全的随机 32 字节字符串，该字符串被编码为十六进制字符串（64 字节）。

```python
def make_uuid() -> 'string':
    """
    生成一个编码为十六进制字符串的全局唯一 256 位 ID，
    用作交易标识符。使用 Python 标准库 secrets 模块
    生成加密安全的随机 32 字节字符串，该字符串被编码为十六进制字符串（64 字节）
    """
    id = secrets.token_hex(32)
    return id
```

## Pytest 入门指南

单元测试为我们提供了代码正确性的保证。因此，我们应该始终努力使用单元测试来提供高覆盖率的代码。从概念上讲，单元测试测试的是代码中一个小的独立片段。这段代码通常是一个函数，但也可以是像单个表达式或一组表达式这样的小片段。

我们将使用 Python 的`Pytest`模块对我们的模块执行单元测试。如果你不熟悉`Pytest`单元测试框架，可以阅读附录 3。附录 3 是关于如何使用`Pytest`的综合教程。本书的最后一个附录提供了所有用于 Helium 的`Pytest`文件。

# rcrypt 单元测试

从你的 Helium 虚拟环境根目录安装 `pytest` 模块：

```
$(virtual) pip install pytest
```

在你的虚拟环境中的 `unit_tests` 目录下创建一个名为 `test_rcrypt.py` 的文件，然后将以下源代码复制到该文件中。进入 `unit_tests` 目录并运行测试：

```
$(virtual) pytest test_rcrypt.py -s
```

`-s` 限定符是可选的。它用于提供更详细的输出：

```python
"""
针对 rcrypt 模块的 pytest 单元测试
"""
import pytest
import rcrypt
import pdb

@pytest.mark.parametrize("input_string, value", [
('hello world', True),
('the quick brown fox jumped over the sleeping dog', True),
('0', True),
('', True)
])
def test_make_SHA256_hash(input_string, value):
    """
    测试是否创建了 SHA-256 消息摘要
    """
    ret = rcrypt.make_SHA256_hash(input_string)
    assert rcrypt.validate_SHA256_hash(ret) == value

@pytest.mark.parametrize("input_string, value", [
('a silent night and a pale paper moon over the saskatchewan river', 64),
('Farmer Brown and the sheep-dog', 64),
('', 64)
])
def test_SHA256_hash_length(input_string, value):
    """
    测试以十六进制格式创建的 SHA3-256 哈希长度是否为 64 字节
    """
    assert len(rcrypt.make_SHA256_hash('hello world')) == 64

@pytest.mark.parametrize("digest, value", [
("123", False),
("644bcc7e564373040999aac89e7622f3ca71fba1d972fd94a31c3bfbf24e3938", True),
("644bcc7e564373040999aac89e7622f3ca71fba1d972fd94a31c3bfbf24e39380", False),
("644bcc7e564373040999aac89e7622f3ca71fba1d972fd94a31cZbfbf24e3938", False),
('', False),
("644bcc7e564373040999aac89e7622f3caz1fba1d972fd94a31c3bfbf24e3938", False),
])
def test_validate_SHA256_hash(digest, value):
    """
    验证是否生成了有效的 SHA-256 消息摘要格式
    """
    assert rcrypt.validate_SHA256_hash(digest) == value

@pytest.mark.parametrize("string_input, value", [
("0", True),
('andromeda galaxy', True),
('Farmer Brown and the sheep-dog', True),
('0', True)
])
def test_make_RIPEMD160_hash(string_input, value):
    """
    验证是否生成了有效的 RIPEMD-160 消息摘要格式
    """
    ret = rcrypt.make_RIPEMD160_hash(string_input)
    assert rcrypt.validate_RIPEMD160_hash(ret) == value

@pytest.mark.parametrize("hash, value", [
('123456789098765432169Q156c79FFa1200CCB1A', False),
("lkorkflfor4flgofmr", False),
('off0099ijf87', False),
('1234567890A87654321690156c79FFa1200CCB1A', True),
])
def test_validate_RIPEMD160_hash(hash, value):
    """
    验证一个字符串是否为 RIPEMD-160 消息摘要格式
    """
    assert rcrypt.validate_RIPEMD160_hash(hash) == value

def test_make_ecc_keys():
    """
    测试 ECC 公私钥对
    """
    ecc_keys = rcrypt.make_ecc_keys()
    assert ecc_keys[0].find("BEGIN PRIVATE KEY") >= 0
    assert ecc_keys[0].find("END PRIVATE KEY") >= 0
    assert ecc_keys[0].find("END PRIVATE KEY") > ecc_keys[0].find("BEGIN PRIVATE KEY")
    assert ecc_keys[1].find("BEGIN PUBLIC KEY") >= 0
    assert ecc_keys[1].find("END PUBLIC KEY") >= 0
    assert ecc_keys[1].find("END PUBLIC KEY") > ecc_keys[1].find("BEGIN PUBLIC KEY")

@pytest.mark.parametrize("message, value", [
('50bfee49c706d766411777aac1c9f35456c33ecea2afb4f3c8f1033b0298bdc9', True),
('6e6404d8693aea119a8ef7cf82c56fe96555d8df2c36c2b9e325411fbef62014', True),
('ba7816bf8f01cfea414140de5dae2223b00361a396177a9cb410ff61f20015ad', True),
('hello world', True) ,
])
def test_sign_message(message, value):
    """
    对消息进行数字签名，然后验证它
    """
    ecc_tuple = rcrypt.make_ecc_keys()
    priv_key  = ecc_tuple[0]
    pub_key   = ecc_tuple[1]
    sig = rcrypt.sign_message(priv_key, message)
    ret = rcrypt.verify_signature(pub_key, message, sig)
    assert ret == value

@pytest.mark.parametrize("prefix, value", [
("a", False),
("1", True),
("Q", False),
("qwerty", False),
("", False),
])
def test_make_address(prefix, value):
    """
    测试从种子生成 Helium 地址
    """
    ret = rcrypt.make_address(prefix)
    assert rcrypt.validate_address(ret) == value

@pytest.mark.parametrize("length, ret", [
(64, True),
(32, False)
])
def test_make_uuid(length, ret):
    """
    测试 ID 生成
    """
    id = rcrypt.make_uuid()
    assert (len(id) == length) == ret
```

你应该会看到 33 个单元测试通过。

## Python 日志记录器

你会注意到，我们广泛使用 Python 日志记录器来协助调试源代码。以下示例演示了其用法：

```python
import logging
logging.basicConfig(filename="debug.log", format='%(asctime)s:%(levelname)s:%(message)s',
                    level=logging.DEBUG)
...
def adder(x, y):
    z = x + y
    logging.debug("This is a debug message")
    logging.info("Informational message, output = " + str(z))
    if z != x + y:
        logging.error("An error has happened!")
    return z
```

`logging.basicConfig` 函数的参数指定将所有日志输出写入 `debug.log` 文件，并且所有严重级别为 `logging.DEBUG` 或更高的日志消息都将被记录。按严重性递增顺序排列的日志级别为：

```
DEBUG, INFO, WARNING, ERROR, CRITICAL
```

日志记录器的第二个参数是可选的；它指定了日志记录器输出的格式。

一旦设置了日志记录器的配置，我们可以像这样为该日志记录器指定一条日志消息：

```
logger.SEVERITY_LEVEL("Some String %s: %s", message_1, message_2)
```

`SEVERITY_LEVEL` 是一个日志级别：`DEBUG`、`INFO`、...、`CRITICAL`。

例如，这将生成如下输出：

```
2020-02-01 16:05:03,969:SEVERITY_LEVEL:validate_block: nonce is negative
```

默认情况下，日志记录器会将消息追加到日志文件。我们可以通过指定可选的 `filemode` 参数来指示日志记录器覆盖现有日志文件：

```python
logging.basicConfig(filename="debug.log", filemode='w', level=DEBUG)
```

# Helium 区块结构

在本节中，我们将讨论 Helium 区块的结构。回顾第 6 章，你应记得区块链是一个经过加密验证的有序且不可变的区块集合。每个 Helium 区块都是一个交易的容器。

区块是如下的字典数据结构（尖括号内描述的是区块属性的类型）：

```
{
"prevblockhash":   
"version":         
"timestamp":       
"difficulty_bits": 
"nonce":           
"merkle_root":     
"height":          
"tx":              
}
```

在 Python 中，整数是一种有符号整数。Python 3 消除了普通整数与长整数之间的区别。整数可以有任意大小，仅受内存限制。Python 可以执行任意大小的整数算术运算。

`prevblockhash` 是前一区块头部的 SHA-256 消息摘要，以十六进制字符串格式表示。该值用于检测前一区块是否被篡改。创世区块的 `prevblockhash` 值是空字符串。

区块属性的子集 `prevblockhash`、`version`、`timestamp`、`difficulty_bits`、`nonce` 和 `merkle_root` 被称为区块头部。区块头部不包含交易列表。由于 Merkle 根包含在头部中，我们可以仅通过对头部计算 SHA-256 消息摘要来检验整个区块（包括交易）的有效性。

`version` 是该区块所属 Helium 的版本号。该属性在 Helium 配置模块中设置，值为“1”。

`timestamp` 是区块创建时的 Unix 时间戳（纪元时间）。纪元时间是指自格林威治标准时间（即 UTC）1970 年 1 月 1 日午夜以来所经过的秒数。它标志着 Unix 的诞生。

`difficulty_bits` 是 Helium 配置模块中定义的 `DIFFICULTY_BITS` 值。这是该区块被挖掘时的值。请注意，`DIFFICULTY_BITS` 参数会随着平均挖矿时间调整至十分钟而周期性变化。

`nonce` 是在 Helium 挖矿算法中使用的数字。

`merkle_root` 是一个 SHA-256 十六进制字符串。Merkle 根使我们能够确保区块中的交易未被篡改。它还能让我们判断特定交易是否存在于该区块中。我们将在后续章节中讨论这个值。

`height` 是该区块的高度。区块链中的区块按照分布式共识算法有序排列，可以形象地看作是逐个堆叠。第一个区块，即创世区块，其高度为 0。

`tx` 是交易列表。列表中的交易数量受区块最大允许大小的限制。在 Helium 配置模块中，此值设置为 1 MB。

既然我们已经定义了区块，我们就可以定义一个 Helium 区块链：

```
blockchain = []
```

区块链是一个列表，每个列表元素都是一个区块。^(⁴¹)

# Helium 区块链详解

现在我们准备详解 Helium 区块链代码。将以下程序代码复制到一个名为 `hblockchain.py` 的文件中，并将该文件复制到 `block` 目录下。这并非区块链模块的完整程序代码，因为交易和数据库操作已被抽象化处理。由于我们尚未开发交易和数据库模块的代码，我们将对这些模块的操作使用合成值进行模拟。

Helium 区块链代码使用了 `json` 和 `pickle` 模块。这两个模块都是 Python 标准库的一部分，因此无需安装它们。JSON 是一种数据交换标准，可以将 Python 对象编码为字符串，然后将这些字符串解码回相应的对象。`Pickle` 通过将对象转换为字节序列，并将此序列写入文件来序列化对象。这个过程称为序列化。`Pickle` 可以反序列化文件以恢复原始对象。如果你之前从未使用过 `Pickle`，附录 5 提供了关于 Pickle 用法的简明介绍。

`add_block` 函数将一个区块添加到 Helium 区块链中。该函数接收一个区块。函数会检查区块属性是否具有有效值。特别地，它验证区块中包含的交易是否有效。如果区块无效，函数返回假值。如果区块有效，函数通过 `Pickle` 模块将区块序列化到一个原始字节文件中。然后，该区块被添加到内存中的区块链列表中。

`serialize_block` 函数使用 `Pickle` 将区块序列化到 `data` 目录下。文件名构造方式如下：

```
filename = "block_" + block_height + ".dat"
```

`read_block` 函数接收一个区块链中的索引，并返回该索引对应的区块。如果索引越界，将抛出异常。

`blockheader_hash` 函数接收一个区块，并返回区块头部的 SHA-256 消息摘要（十六进制字符串）。再次注意，由于 Merkle 根可用于检验区块中的交易是否被篡改，我们无需对整个区块内容计算 SHA-256 加密哈希。仅对包含 Merkle 根的区块头部计算该值就足够了。

`validate_block` 函数测试区块是否具有有效值。该函数还通过比较 SHA-256 消息摘要来验证上一个区块。

`hblockchain` 及其他 Helium 模块的最终程序代码清单可参阅本书附录 10。

# Helium 区块链模块

`hblockchain.py`：该模块用于创建和维护 Helium 区块链。以下是该模块的部分实现。

```python
import rcrypt
import hconfig
import json
import pickle
import pdb
import logging
import os
```

将调试信息记录到 `debug.log` 文件中。

```python
logging.basicConfig(filename="debug.log", filemode="w",
                    format='%(asctime)s:%(levelname)s:%(message)s', level=logging.DEBUG)
```

## 区块结构

一个区块是一个具有以下结构的 Python 字典。属性类型用尖括号表示。

```
{
"prevblockhash":   
"version":         
"timestamp":       
"difficulty_bits": 
"nonce":           
"merkle_root":     
"height":          
"tx":              
}
```

区块链是一个列表，其中每个列表元素是一个区块。当被矿工使用时，这也被称为主区块链。

```python
blockchain = []
```

## 函数

### `add_block(block: "dictionary") -> "bool"`

`add_block`：向区块链添加一个区块。接收一个区块。会检查区块属性的有效性，并测试区块中每笔交易的有效性。如果没有错误，该区块将以原始字节序列的形式写入文件。然后将该区块添加到区块链中。如果区块成功添加到区块链则返回 `True`，否则返回 `False`。

```python
def add_block(block: "dictionary") -> "bool":
    try:
        # 验证接收到的区块参数
        if validate_block(block) == False:
            raise(ValueError("block validation error"))
        # 将区块序列化到文件
        if (serialize_block(block) == False):
            raise(ValueError("serialize block error"))
        # 将区块添加到内存中的区块链
        blockchain.append(block)
    except Exception as err:
        print(str(err))
        logging.debug('add_block: exception: ' + str(err))
        return False
    return True
```

### `serialize_block(block: "dictionary") -> "bool"`

`serialize_block`：使用 pickle 将区块序列化到文件。如果区块序列化成功则返回 `True`，否则返回 `False`。

```python
def serialize_block(block: "dictionary") -> "bool":
    index = len(blockchain)
    filename = "block_" + str(index) + ".dat"
    # 创建区块文件并序列化区块
    try:
        f = open(filename, 'wb')
        pickle.dump(block, f)
    except Exception as error:
        logging.debug("Exception: %s: %s", "serialize_block", error)
        f.close()
        return False
    f.close()
    return True
```

### `read_block(blockno: 'long') -> "dictionary or False"`

`read_block`：接收一个指向 Helium 区块链的索引。返回一个区块，如果该区块不存在则返回 `False`。

```python
def read_block(blockno: 'long') -> "dictionary or False":
    try:
        block = blockchain[blockno]
        return block
    except Exception as error:
        logging.debug("Exception: %s: %s", "read_block", error)
        return False
    return block
```

### `blockheader_hash(block: 'dictionary') -> "False or String"`

`blockheader_hash`：计算并返回区块头的 SHA-256 消息摘要，格式为十六进制字符串。接收一个需要计算区块头哈希的区块。如果出错则返回 `False`，否则返回一个 SHA-256 十六进制字符串。区块头包含以下区块字段：(1) `version`，(2) `prevblockhash`，(3) `merkle_root`，(4) `timestamp`，(5) `difficulty_bits`，以及 (6) `nonce`。

```python
def blockheader_hash(block: 'dictionary') -> "False or String":
    try:
        hash = rcrypt.make_SHA256_hash(block['version'] + block['prevblockhash'] +
                                       block['merkle_root'] + str(block['timestamp']) +
                                       str(block['difficulty_bits']) + str(block['nonce']))
    except Exception as error:
        logging.debug("Exception:%s: %s", "blockheader_hash", error)
        return False
    return hash
```

### `validate_block(block: "dictionary") -> "bool"`

`validate_block`：接收一个区块并验证其所有属性是否具有有效值。如果区块有效则返回 `True`，否则返回 `False`。

```python
def validate_block(block: "dictionary") -> "bool":
    try:
        if type(block) != dict:
            raise(ValueError("block type error"))
        # 验证标量区块属性
        if type(block["version"]) != str:
            raise(ValueError("block version type error"))
        if block["version"] != hconfig.conf["VERSION_NO"]:
            raise(ValueError("block wrong version"))
        if type(block["timestamp"]) != int:
            raise(ValueError("block timestamp type error"))
        if block["timestamp"] < 0:
            raise(ValueError("block timestamp error"))
        if block["height"] < 0:
            raise(ValueError("block height error"))
        # 高度必须递增 1
        if block["height"] > 0:
            if block["height"] != blockchain[-1]["height"] + 1:
                raise(ValueError("block height is not in order"))
        # 区块长度必须小于配置模块中指定的最大区块大小。
        # json.dumps 将区块转换为 json 格式字符串。
        if len(json.dumps(block)) > hconfig.conf["MAX_BLOCK_SIZE"]:
            raise(ValueError("block length error"))
        # 验证 merkle_root。
        if block["merkle_root"] != merkle_root(block["tx"], True):
            raise(ValueError("merkle roots do not match"))
        # 通过比较消息摘要来验证前一个区块。
        # 创世区块没有前驱区块
        if block["height"] > 0:
            if block["prevblockhash"] != blockheader_hash(blockchain[block["height"]-1]):
                raise(ValueError("previous block header hash does not match"))
        else:
            if block["prevblockhash"] != "":
                raise(ValueError("genesis block has prevblockhash"))
        # 创世区块没有任何输入交易
        if block["height"] == 0 and block["tx"][0]["vin"] != []:
            raise(ValueError("missing coinbase transaction"))
        # 除创世区块外的其他区块必须至少包含两笔交易：一笔 coinbase 交易和至少一笔其他交易
        if block["height"] > 0 and len(block["tx"]) < 2:
            raise(ValueError("block does not have a coinbase transaction"))
    except Exception as err:
        logging.debug("validate_block: exception: " + str(err))
        return False
    return True
```

### `merkle_root(tx_list: "list of transactions", first: "bool") -> "string or False"`

`merkle_root`：计算交易列表的 Merkle 根。接收一个交易列表和一个布尔标志。该函数自底向上计算 Merkle 根。如果是首次调用，它会计算每笔交易的哈希。然后这些哈希被配对并连接，以形成树的下一层。此过程递归重复，直到只剩下一个哈希。该哈希即为 Merkle 根。返回 Merkle 根，如果出错则返回 `False`。

```python
def merkle_root(tx_list: "list of transactions", first: "bool") -> "string or False":
    pass
```

### `merkle_tree(tx_list: "list of transactions", first: "bool") -> "bool or string"`

`merkle_tree`：计算交易列表的 Merkle 根。接收一个交易列表和一个布尔标志，用于指示该函数是首次被调用还是函数内部的递归调用。返回 Merkle 树的根，如果出错则返回 `False`。

```python
def merkle_tree(tx_list: "list of transactions", first: "bool") -> "bool or string":
    pass
```

## Helium 区块链单元测试

将以下代码复制到一个名为`test_blockchain.py`的文件中，并将其保存在`unit_tests`目录中。现在你可以切换到该目录并运行所有测试：

```bash
$(virtual) pytest test_blockchain.py -v -s
```

你应该会看到 23 个单元测试通过。

由于我们目前对 Helium 区块链的实现尚不完整，以下测试套件是一个受限集。`blockchain.py`的完整单元测试集可在本书的附录 9 中找到。

```python
"""
测试区块链功能
交易数值为合成数据。
"""
import pytest
import hblockchain
import hconfig
import rcrypt
import time
import os
import pdb
import secrets

def teardown_module():
    """
    所有测试执行完毕后，移除已创建的任何区块
    """
    os.system("rm *.dat")
    hblockchain.blockchain.clear()

###################################################
### 生成用于测试的合成随机交易
###################################################
def make_random_transaction(block_height):
    tx = {}
    tx["version"] =  "1"
    tx["transactionid"] = rcrypt.make_uuid()
    tx["locktime"] = secrets.randbelow(hconfig.conf["MAX_LOCKTIME"])
    # 上一笔交易的公私钥对
    prev_keys = rcrypt.make_ecc_keys()
    # 本笔交易的公私钥对
    keys = rcrypt.make_ecc_keys()
    # 构建 vin
    tx["vin"] = []
    if block_height > 0:
        ctr = secrets.randbelow(hconfig.conf["MAX_INPUTS"]) + 1
        ind = 0
        while ind < ctr:
            signed = rcrypt.sign_message(prev_keys[0], prev_keys[1])
            ScriptSig = []
            ScriptSig.append(signed[0])
            ScriptSig.append(prev_keys[1])
            tx["vin"].append({
                "txid": rcrypt.make_uuid(),
                "vout_index": ctr,
                "ScriptSig": ScriptSig
            })
            ind += 1
    # 构建 Vout
    tx["vout"] = []
    ctr = secrets.randbelow(hconfig.conf["MAX_OUTPUTS"]) + 1
    ind = 0
    while ind < ctr:
        ScriptPubKey = []
        ScriptPubKey.append("DUP")
        ScriptPubKey.append("HASH-160")
        ScriptPubKey.append(keys[1])
        ScriptPubKey.append("EQ_VERIFY")
        ScriptPubKey.append("CHECK-SIG")
        tx["vout"] = {
            "value": secrets.randbelow(10000000) + 1000000,   # 氦分（helium cents）
            "ScriptPubKey": ScriptPubKey
        }
        ind += 1
    return tx

#############################################
### 构建三个用于测试的合成区块
#############################################
block_0 = {
    "prevblockhash": "",
    "version": "1",
    "timestamp": 0,
    "difficulty_bits": 20,
    "nonce": 0,
    "merkle_root": rcrypt.make_SHA256_hash('msg0'),
    "height": 0,
    "tx": [make_random_transaction(0)]
}

block_1 = {
    "prevblockhash": hblockchain.blockheader_hash(block_0),
    "version": "1",
    "timestamp": 0,
    "difficulty_bits": 20,
    "nonce": 0,
    "merkle_root": rcrypt.make_SHA256_hash('msg1'),
    "height": 1,
}
block_1["tx"] = []
block_1["tx"].append(make_random_transaction(1))
block_1["tx"].append(make_random_transaction(1))

block_2 = {
    "prevblockhash": hblockchain.blockheader_hash(block_1),
    "version": "1",
    "timestamp": 0,
    "difficulty_bits": 20,
    "nonce": 0,
    "merkle_root": rcrypt.make_SHA256_hash('msg2'),
    "height": 2,
}
block_2["tx"] = []
block_2["tx"].append(make_random_transaction(2))
block_2["tx"].append(make_random_transaction(2))

def test_block_type(monkeypatch):
    """
    测试区块的类型
    """
    monkeypatch.setattr(hblockchain, "merkle_root", lambda x, y: \
        rcrypt.make_SHA256_hash('msg0'))
    assert hblockchain.validate_block(block_0) == True

def test_add_good_block(monkeypatch):
    """
    测试添加一个有效区块
    """
    monkeypatch.setattr(hblockchain, "merkle_root", lambda x, y: \
        rcrypt.make_SHA256_hash('msg0'))
    assert hblockchain.add_block(block_0) == True
    monkeypatch.setattr(hblockchain, "merkle_root", lambda x, y: \
        rcrypt.make_SHA256_hash('msg1'))
    assert hblockchain.add_block(block_1) == True
    hblockchain.blockchain.clear()

def test_missing_version(monkeypatch):
    """
    测试缺少版本号
    """
    monkeypatch.setattr(hblockchain, "merkle_root", lambda x, y: \
        rcrypt.make_SHA256_hash('msg1'))
    monkeypatch.setitem(block_1, "version", "")
    assert hblockchain.add_block(block_1) == False

def test_version_bad(monkeypatch):
    """
    测试未知版本号
    """
    monkeypatch.setattr(hblockchain, "merkle_root", lambda x, y: \
        rcrypt.make_SHA256_hash('msg1'))
    monkeypatch.setitem(block_1, "version", -1)
    assert hblockchain.add_block(block_1) == False

def test_bad_timestamp_type(monkeypatch):
    """
    测试错误的时间戳类型
    """
    monkeypatch.setattr(hblockchain, "merkle_root", lambda x, y: \
        rcrypt.make_SHA256_hash('msg1'))
    monkeypatch.setitem(block_1, "timestamp", "12345")
    assert hblockchain.add_block(block_1) == False

def test_negative_timestamp(monkeypatch):
    """
    测试负数时间戳
    """
    monkeypatch.setattr(hblockchain, "merkle_root", lambda x, y: \
        rcrypt.make_SHA256_hash('msg0'))
    monkeypatch.setitem(block_0, "timestamp", -2)
    assert hblockchain.add_block(block_0) == False

def test_missing_timestamp(monkeypatch):
    """
    测试缺少时间戳
    """
    monkeypatch.setattr(hblockchain, "merkle_root", lambda x, y: \
        rcrypt.make_SHA256_hash('msg1'))
    monkeypatch.setitem(block_1, "timestamp", "")
    assert hblockchain.add_block(block_1) == False

def test_block_height_type(monkeypatch):
    """
    测试区块高度参数的类型
    """
    monkeypatch.setattr(hblockchain, "merkle_root", lambda x, y: \
        rcrypt.make_SHA256_hash('msg0'))
    monkeypatch.setitem(block_0, "height", "0")
    hblockchain.blockchain.clear()
    assert hblockchain.add_block(block_0) == False

def test_bad_nonce(monkeypatch):
    """
    测试负数 nonce
    """
    monkeypatch.setattr(hblockchain, "merkle_root", lambda x, y: \
        rcrypt.make_SHA256_hash('msg1'))
    monkeypatch.setitem(block_1, "nonce", -1)
    assert hblockchain.add_block(block_1) == False

def test_missing_nonce(monkeypatch):
    """
    测试缺少 nonce
    """
    monkeypatch.setattr(hblockchain, "merkle_root", lambda x, y: \
        rcrypt.make_SHA256_hash('msg0'))
    monkeypatch.setitem(block_0, "nonce", "")
    assert hblockchain.add_block(block_0) == False

def test_block_nonce_type(monkeypatch):
    """
    测试 nonce 类型错误
    """
    monkeypatch.setattr(hblockchain, "merkle_root", lambda x, y: \
        rcrypt.make_SHA256_hash('msg0'))
    monkeypatch.setitem(block_0, "nonce", "0")
    assert hblockchain.add_block(block_0) == False

def test_negative_difficulty_bit(monkeypatch):
    """
    测试负数难度值
    """
    monkeypatch.setattr(hblockchain, "merkle_root", lambda x, y: \
        rcrypt.make_SHA256_hash('msg1'))
    monkeypatch.setitem(block_1, "difficulty_bits", -5)
    assert hblockchain.add_block(block_1) == False

def test_difficulty_type(monkeypatch):
    """
    测试难度值类型错误
    """
    monkeypatch.setattr(hblockchain, "merkle_root", lambda x, y: \
        rcrypt.make_SHA256_hash('msg0'))
    monkeypatch.setitem(block_0, "difficulty_bits", "20")
    assert hblockchain.add_block(block_0) == False

def test_missing_difficulty_bit(monkeypatch):
    """
    测试缺少难度值
    """
    monkeypatch.setattr(hblockchain, "merkle_root", lambda x, y: \
        rcrypt.make_SHA256_hash('data'))
    monkeypatch.setitem(block_1, "difficulty_bits", '')
    assert hblockchain.add_block(block_1) == False

def test_read_genesis_block(monkeypatch):
    """
    测试从区块链读取创世区块
    """
    hblockchain.blockchain.clear()
    monkeypatch.setattr(hblockchain, "merkle_root", lambda x, y: \
        rcrypt.make_SHA256_hash('msg0'))
    hblockchain.add_block(block_0)
    assert hblockchain.read_block(0) == block_0
    hblockchain.blockchain.clear()

def test_genesis_block_height(monkeypatch):
    """
    测试创世区块高度
    """
    hblockchain.blockchain.clear()
    monkeypatch.setattr(hblockchain, "merkle_root", lambda x, y: \
        rcrypt.make_SHA256_hash('msg0'))
    block_0["height"] = 0
    assert hblockchain.add_block(block_0) == True
    blk = hblockchain.read_block(0)
    assert blk != False
    assert blk["height"] == 0
    hblockchain.blockchain.clear()

def test_read_second_block(monkeypatch):
    """
    测试从区块链读取第二个区块
    """
    hblockchain.blockchain.clear()
    assert len(hblockchain.blockchain) == 0
    monkeypatch.setattr(hblockchain, "merkle_root", lambda x, y: \
        rcrypt.make_SHA256_hash('msg0'))
    monkeypatch.setitem(block_1, "prevblockhash", hblockchain.blockheader_hash(block_0))
    ret = hblockchain.add_block(block_0)
    assert ret == True
    monkeypatch.setattr(hblockchain, "merkle_root", lambda x, y: \
        rcrypt.make_SHA256_hash('msg1'))
    ret = hblockchain.add_block(block_1)
    assert ret == True
    block = hblockchain.read_block(1)
    assert block != False
    hblockchain.blockchain.clear()

def test_block_height(monkeypatch):
    """
    测试第二个区块的高度
    """
    hblockchain.blockchain.clear()
    monkeypatch.setattr(hblockchain, "merkle_root", lambda x, y: \
        rcrypt.make_SHA256_hash('msg0'))
    monkeypatch.setitem(block_0, "height", 0)
    monkeypatch.setitem(block_0, "prevblockhash", "")
    monkeypatch.setitem(block_1, "height", 1)
    monkeypatch.setitem(block_1, "prevblockhash", hblockchain.blockheader_hash(block_0))
    assert hblockchain.add_block(block_0) == True
    monkeypatch.setattr(hblockchain, "merkle_root", lambda x, y: \
        rcrypt.make_SHA256_hash('msg1'))
    assert hblockchain.add_block(block_1) == True
    blk = hblockchain.read_block(1)
    assert blk != False
    assert blk["height"] == 1
    hblockchain.blockchain.clear()

def test_block_size(monkeypatch):
    """
    区块大小必须小于 hconfig["MAX_BLOCKS"]
    """
    monkeypatch.setattr(hblockchain, "merkle_root", lambda x, y: \
        rcrypt.make_SHA256_hash('msg0'))
    arry = []
    filler = "0" * 2000000
    arry.append(filler)
    monkeypatch.setitem(block_0, "tx", arry)
    hblockchain.blockchain.clear()
    assert hblockchain.add_block(block_0) == False

def test_genesis_block_prev_hash(monkeypatch):
    """
    测试创世区块的上一区块哈希值是否为空
    """
    hblockchain.blockchain.clear()
    monkeypatch.setattr(hblockchain, "merkle_root", lambda x, y: \
        rcrypt.make_SHA256_hash('msg0'))
    monkeypatch.setitem(block_0, "height", 0)
    monkeypatch.setitem(block_0, "prevblockhash", rcrypt.make_uuid() )
    assert len(hblockchain.blockchain) == 0
    assert hblockchain.add_block(block_0) == False

def test_computes_previous_block_hash(monkeypatch):
    """
    测试上一区块哈希值格式是否正确
    """
    val = hblockchain.blockheader_hash(block_0)
    rcrypt.validate_SHA256_hash(val) == True

def test_invalid_previous_hash(monkeypatch):
    """
    测试区块的 prevblockhash 无效
    """
    hblockchain.blockchain.clear()
    monkeypatch.setattr(hblockchain, "merkle_root", lambda x, y: \
         rcrypt.make_SHA256_hash('msg0'))
    monkeypatch.setitem(block_2, "prevblockhash", \
        "188a1fd32a1f83af966b31ca781d71c40f756a3dc2a7ac44ce89734d2186f632")
    hblockchain.blockchain.clear()
    assert hblockchain.add_block(block_0) == True
    monkeypatch.setattr(hblockchain, "merkle_root", lambda x, y: \
        rcrypt.make_SHA256_hash('msg1'))
    assert hblockchain.add_block(block_1) == True
    monkeypatch.setattr(hblockchain, "merkle_root", lambda x, y: \
        rcrypt.make_SHA256_hash('msg2'))
    assert hblockchain.add_block(block_2) == False
    hblockchain.blockchain.clear()

def test_no_consecutive_duplicate_blocks(monkeypatch):
    """
    测试不能连续向区块链添加两个相同的区块
    """
    hblockchain.blockchain.clear()
    monkeypatch.setattr(hblockchain, "merkle_root", lambda x, y: \
        rcrypt.make_SHA256_hash('msg0'))
    assert hblockchain.add_block(block_0) == True
    monkeypatch.setattr(hblockchain, "merkle_root", lambda x, y: \
        rcrypt.make_SHA256_hash('msg1'))
    monkeypatch.setitem(block_1, "prevblockhash", hblockchain.blockheader_hash(block_0))
    assert hblockchain.add_block(block_1) == True
    monkeypatch.setitem(block_1, "height", 2)
    assert hblockchain.add_block(block_1) == False
    hblockchain.blockchain.clear()
```

## 结论

在本章中，我们开发了 `rcrypt` 模块，该模块封装了 Helium 使用的加密函数。我们还为加密模块编写了单元测试。接着，我们为 Helium 区块链编写了程序代码。`hblockchain` 模块封装了 Helium 区块链的功能。最后，我们编写了一些单元测试来验证区块链模块。

在下一章中，我们将把注意力转向加密货币交易的处理。

脚注 1 2 3 4 5