#  {'last_seen': 1589780748, 'ip': '192.168.0.1', 'port': 8888}
```

如果某个字段不正确或验证失败，Marshmallow 会抛出一个 `ValidationError`（验证错误）。

让我们尝试对结果进行序列化：

```python
serialized = Peer().dumps(some_payload)
