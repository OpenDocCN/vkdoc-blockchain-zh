# "{'last_seen': 1589780748, 'ip': '192.168.0.1', 'port': 8888}"
```

如果你仍然不确定 Marshmallow 的工作原理，或者不明白为什么它能给我们省去很多麻烦，那么很值得去查阅一下它的文档，因为它们的网站上提供了大量的示例：[`https://marshmallow.readthedocs.io`](https://marshmallow.readthedocs.io)

**注意**

大多数序列化库的一个共同特点是坚持使用 `loads` 和 `dumps` 作为方法名：`loads` 从字符串反序列化；`load` 从对象反序列化；`dumps` 序列化为字符串；`dump` 序列化为对象。

### 实现并验证类型

让我们创建三个新文件：`funcoin/types.py`、`funcoin/messages.py` 和 `funcoin/utils.py`，分别用来存放我们的*类型（types）*、*消息（messages）*和额外的辅助函数。你的文件夹结构应该如下所示：

```
.
├── funcoin
│     ├── __init__.py
│     ├── blockchain.py
│     ├── connections.py
│     ├── factories.py
│     ├── messages.py
│     ├── peers.py
│     ├── server.py
│     ├── transactions.py
│     ├── types.py
│     └── utils.py
├──__init__.py
├── node.py
├── poetry.lock
└── pyproject.toml
```

首先，我们在 `funcoin/schema.py` 中定义 Marshmallow 模式。

```python
1  import json
2  from time import time
3  
4  from marshmallow import Schema, fields, validates_schema, ValidationError
5  
6  
7  class Transaction(Schema):
8      timestamp = fields.Int()
9      sender = fields.Str()
10     receiver = fields.Str()
11     amount = fields.Int()
12     signature = fields.Str()
13  
14     class Meta:
15         ordered = True
16  
17  
18  class Block(Schema):
19      mined_by = fields.Str(required=False)
20      transactions = fields.Nested(Transaction(), many=True)
21      height = fields.Int(required=True)
22      target = fields.Str(required=True)
23      hash = fields.Str(required=True)
24      previous_hash = fields.Str(required=True)
25      nonce = fields.Str(required=True)
26      timestamp = fields.Int(required=True)
27  
28      class Meta:
29          ordered = True
30  
31      @validates_schema
32      def validate_hash(self, data, **kwargs):
33          block = data.copy()
34          block.pop("hash")
35  
36          if data["hash"] != json.dumps(block, sort_keys=True):
37              raise ValidationError("Fraudulent block: hash is wrong")
38  
39  
40  class Peer(Schema):
41      ip = fields.Str(required=True)
42      port = fields.Int(required=True)
43      last_seen = fields.Int(missing=lambda: int(time()))
```

*代码清单 7-7*
*funcoin/schema.py*

在代码中，我们实现了 `Peer`、`Block` 和 `Transaction` 三个类。

在 `Transaction` 中，我们包含了一个名为 `validate_signature` 的验证方法。它通过 `@validates_schema` 装饰器进行标记——这是一个特殊的装饰器，用于指示 Marshmallow 应该在验证过程中运行此函数。这就是我们验证交易的地方：在反序列化时，确保我们反序列化的*任何*交易都是*始终*有效的。

在 `Block` 中，我们也实现了一个验证方法，以确保*任何*消息中包含的*任何*区块都是始终有效的。



### 定义消息（及其模式）

在`funcoin/messages.py`中，我们将实现各种消息及其模式（schema），然后对它们进行测试以确保一致性。这些是我们要实现的类：

*   `PeersMessage`
*   `BlockMessage`
*   `TransactionMessage`
*   `PingPayload`
*   `PingMessage`
*   `Base`

请注意，`PeersMessage`的载荷（payload）是一个`Peer`列表；`BlockMessage`的载荷是一个`Block`；`TransactionMessage`的载荷是一个`Transaction`。这就是我们首先在`types.py`中定义它们的原因。

作为给读者的练习，你可以尝试跳过此处，自行定义每条消息的模式。

以下是完成后它们应有的样子。

```python
1  from marshmallow import Schema, fields, post_load
2  from marshmallow_oneofschema import OneOfSchema
4  from funcoin.schema import Peer, Block, Transaction, Ping
7  class PeersMessage(Schema):
8      payload = fields.Nested(Peer(many=True))
10      @post_load
11      def add_name(self, data, **kwargs):
12          data["name"] = "peers"
13          return data
16  class BlockMessage(Schema):
17      payload = fields.Nested(Block)
19      @post_load
20      def add_name(self, data, **kwargs):
21          data["name"] = "block"
22          return data
25  class TransactionMessage(Schema):
26      payload = fields.Nested(Transaction)
28      @post_load
29      def add_name(self, data, **kwargs):
30          data["name"] = "transaction"
31          return data
34  class PingMessage(Schema):
35      payload = fields.Nested(Ping)
37      @post_load
38      def add_name(self, data, **kwargs):
39          data["name"] = "ping"
40          return data
43  class MessageDisambiguation(OneOfSchema):
44      type_field = "name"
45      type_schemas = {
46          "ping": PingMessage,
47          "peers": PeersMessage,
48          "block": BlockMessage,
49          "transaction": TransactionMessage,
50      }
51      def get_obj_type(self, obj):
52        if isinstance(obj, dict):
53            return obj.get("name")
55  class MetaSchema(Schema):
56      address = fields.Nested(Peer())
57      client = fields.Str()
60  class BaseSchema(Schema):
61      meta = fields.Nested(MetaSchema())
62      message = fields.Nested(MessageDisambiguation())
65  def meta(ip, port, version="funcoin-0.1"):
66      return {
67          "client": version,
68          "address": {
69              "ip": ip,
70              "port": port
71          },
72      }
75  def create_peers_message(external_ip, external_port, peers):
76      return BaseSchema().dumps({
77          "meta": meta(external_ip, external_port),
78          "message": {
79              "name": "peers",
80              "payload":  peers
81          }
82      }
83      )
86  def create_block_message(external_ip, external_port, block):
87      return BaseSchema().dumps({
88          "meta": meta(external_ip, external_port),
89          "message": {
90              "name": "block",
91              "payload": block
92          }
93      }
94      )
96  def create_transaction_message(external_ip, external_port, tx):
97      return BaseSchema().dumps(
98          {
99              "meta": meta(external_ip, external_port),
100             "message": {
101                 "name": "transaction",
102                 "payload": tx,
103             },
104         }
105     )
```

代码清单 7-8: `funcoin/messages.py`

请注意，我们还包含一些辅助函数来创建消息。例如，在第 89 行，我们包含了`create_block_message`，它生成一个包含区块的消息。让我们针对一个区块来测试它：

```python
some_block = {
"mined_by": "some public key",
"transactions":   [],
"height": 123,
"difficulty": 10,
"hash": "some fake hash", "previous_hash":  0,
"nonce":  23,
"timestamp":  238778621,
}
```

如你所见，这不是一个有效的区块；让我们尝试将它加载到`Block()`验证器中：

```python
Block().load(some_block)
marshmallow.exceptions.ValidationError: {'transactions': {'_schema': ['Invalid input type.']}, 'previous_hash': ['Not a valid string.'], 'nonce': ['Not a valid string.']}
```

如你所见，`marshmallow`的描述性相当强，它准确告诉我们区块存在的问题。让我们修复这些问题后再次尝试：

```python
some_block = {
"mined_by": "some public key",
"transactions": [],
"height": 123,
"difficulty": 10,
"hash": "some fake hash",
"previous_hash":  0,
"nonce":  23,
"timestamp":  238778621,
}
Block().load(some_block)
ValidationError: {'_schema': ['Fraudulent block: hash is wrong']}
```

如你所见，`marshmallow`运行了我们的验证函数，该函数正确地判断出我们那个“some fake hash”哈希值显然是错误的。让我们再次尝试：

```python
some_block = {
"mined_by": "some public key",
"height": 123,
"difficulty": 10,
"previous_hash": "some previous hash",
"nonce": "213",
"timestamp": 238778621,
"hash": "a52bfa60bc4ad3d3e9571eab8b28370166f2476e0f1026df219bec07a0a9e2e7"
}
