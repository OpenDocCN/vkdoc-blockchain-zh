# 排版后的文档

叶节点是数据节点，位于叶节点上方的节点存储数据元素的哈希值。此处，`H(A)`表示数据`A`的哈希值。哈希函数可以是`sha256`或`keccak256`，这两种函数在以太坊中常用。借助这些默克尔树，可以通过提供根哈希和“叔伯”节点的哈希值来验证每个节点的完整性。例如，在上图中，若要证明`A`元素的交易有效，用户需要提供`hash_b`、`hash_c`和`hash_d`来计算根节点哈希值，并与记录在根链中的根哈希值进行比较。

以下代码片段演示了上图中默克尔树在 Solidity 中的实现方式。该默克尔树演示程序的源代码位于 GitHub 上。

```
/**
 * *@title 用于 4 个叶节点的 MerkleDemo*
 */
contract MerkleDemo {
    //声明叶节点
    string public leaf_a = "Alice sent 10 tokens to Bob";
    string public leaf_b = "Bob sent 9 tokens to Cathy";
    string public leaf_c = "Cathy sent 8 tokens to David";
    string public leaf_d = "David sent 7 tokens to Eric";

    //将父节点和根节点声明为 32 字节的哈希值
    bytes32 public hash_a;
    bytes32 public hash_b;
    bytes32 public hash_c;
    bytes32 public hash_d;
    bytes32 public hash_cd;
    bytes32 public hash_ab;
    bytes32 public hash_ab_cd;
    bytes32 public hash_root;
```

在上述代码片段中，四个叶节点——`leaf_a`、`leaf_b`、`leaf_c`和`leaf_d`——被定义为`string`类型的变量，用于记录简化后的交易。叶节点的内容经过哈希处理后存储在`bytes32`数据类型的`hash_a`、`hash_b`、`hash_c`和`hash_d`变量中。`hash_a`和`hash_b`合并形成一个名为`hash_ab`的父哈希值。`hash_c`和`hash_d`合并形成父节点`hash_cd`。最后，`hash_ab`和`hash_cd`合并形成`hash_ab_cd`。`hash_ab_cd`是顶级节点，等同于`hash_root`。

`MerkleDemo`智能合约的构造函数按如下方式构建默克尔树：

```
/*
 * *用于构建默克尔树的构造函数*
 */
constructor() public {
    //使用 sha256 哈希函数构建默克尔树
    //abi.encodePacked 用于连接两个子哈希值
    hash_a = sha256(abi.encodePacked(leaf_a));
    hash_b = sha256(abi.encodePacked(leaf_b));
    hash_c = sha256(abi.encodePacked(leaf_c));
    hash_d = sha256(abi.encodePacked(leaf_d));
    hash_ab = sha256(abi.encodePacked(hash_a, hash_b));
    hash_cd = sha256(abi.encodePacked(hash_c, hash_d));
    hash_ab_cd = sha256(abi.encodePacked(hash_ab, hash_cd));
    hash_root = hash_ab_cd;
}
```

在上述构造函数代码中，使用了 Solidity 的`sha256`函数对叶节点或非叶节点进行哈希处理。`abi.encodePacked`函数用于连接两个元素，并对合并后的结果进行哈希处理以得到父节点。一旦`hash_root`生成，它会被保存到根区块链中，并可用于验证用户或操作员所请求的默克尔树中的节点。

若要检查某个叶节点所代表的交易是否存在于默克尔树中，只需使用以下`checkMerkleTree`函数：

```
/**
 * *检查某个叶节点是否有效*
 * *@param _leaf 待检查的叶节点*
 * *@param _index 该叶节点在树中的索引*
 * *@param _rootHash 树的根哈希值*
 * *@param _proof 证明该叶节点存在于树中的默克尔证明*
 * *@return 如果叶节点存在于树中则返回 true，否则返回 false*
 */
function checkMerkleTree(
    string memory _leaf,
    uint256 _index,
    bytes32 _rootHash,
    bytes memory _proof
) public pure returns (bool) {
    // 检查 _proof 是否为一个或多个 bytes32
    require(_proof.length % 32 == 0, "proof length not valid.");
    // 计算默克尔根哈希值
    bytes32 proofElement;
    bytes32 parentHash = sha256(abi.encodePacked(_leaf));
    uint256 index = _index;

    //遍历证明中的每个 bytes32 元素
    for (uint256 i = 32; i <= _proof.length; i += 32) {
        assembly {
            proofElement = mload(add(_proof, i))
        }
        if (index % 2 == 0) { // 叶节点在左侧
            parentHash = sha256(abi.
```



*   `encodePacked(parentHash, proofElement));`
*   `} else { //叶子节点在右侧`
*   `parentHash = sha256(abi.`
*   `encodePacked(proofElement, parentHash));`
*   `}`

## 第 9 章 第 2 层与以太坊 2.0

*   `index = index / 2; //进入下一层级`
*   `}`
*   `// 如果 parentHash 等于 _rootHash，则验证通过。`
*   `return parentHash == _rootHash;`
*   `}`

`checkMerkleTree`函数接收一个叶子节点合约（即一笔交易）、一个显示叶子节点位置的索引、一个根哈希以及一个证明，以计算由叶子节点和证明计算出的根哈希是否与记录相匹配。如果计算出的哈希根与记录的一致，则叶子节点和证明被视为有效。否则，叶子节点或其代表的交易被视为被篡改。

接下来，我们有一个`testCasesdemo`函数来验证未篡改和篡改情况下的叶子节点`c`和`d`，如下所示：

```
// 测试默克尔树中的部分叶子节点
function testCasesdemo() public payable returns(string
memory, string memory, string memory, string memory){
   //待验证的有效和无效交易信息
   string memory test_leaf_c = "Cathy sent 8 tokens
   to David";
   string memory test_leaf_d = "David sent 7 tokens
   to Eric";
   // 以下两笔交易被篡改，不应通过验证
   string memory test_leaf_c_tempered = "Cathy sent 100000
   tokens to David";
   string memory test_leaf_d_tempered = "David sent 100000
   tokens to Eric";
   // leaf_a, leaf_b, leaf_c, leaf_d 的索引分别为 0, 1, 2, 3
```

## 第 9 章 第 2 层与以太坊 2.0

```
   // 使用 test_index 变量表示索引
   uint test_index = 2;
   // 为 leaf_c 构建默克尔证明
   bytes memory merkle_proof = abi.encodePacked(hash_d,
   hash_ab);
   bool result = checkMerkleTree(test_leaf_c, test_index,
   hash_root, merkle_proof);
   string memory return_leaf_c = string(abi.
   encodePacked(test_leaf_c, ": ",result?"true":"false"));
   // 对篡改后的 leaf_c 执行相同验证
   result = checkMerkleTree(test_leaf_c_tempered, test_
   index, hash_root, merkle_proof);
   string memory return_leaf_c_tempered =
   string(abi.encodePacked(test_leaf_c_tempered, ":"
   ,result?"true":"false"));
   // 对 leaf_d 执行相同操作，与 leaf_c 类似
   test_index = 3;
   merkle_proof = abi.encodePacked(hash_c, hash_ab);
   result = checkMerkleTree(test_leaf_d, test_index, hash_
   root, merkle_proof);
   string memory return_leaf_d = string(abi.
   encodePacked(test_leaf_d, ": ",result?"true":"false"));
   result = checkMerkleTree(test_leaf_d_tempered, test_
   index, hash_root, merkle_proof);
   string memory return_leaf_d_tempered =
   string(abi.encodePacked(test_leaf_d_tempered, ":"
   ,result?"true":"false"));
   // 返回并输出结果
   return (return_leaf_c, return_leaf_c_tempered, return_
   leaf_d, return_leaf_d_tempered);
}
```

## 第 9 章 第 2 层与以太坊 2.0

此测试方法构造了有效的`test_leaf_c`和`test_leaf_d`交易。同时，它将这两笔交易修改为`test_leaf_c_tempered`和`test_leaf_d_tempered`，并将转移的代币数量更改为更大的值。还构造了每个节点的证明并指定了交易的索引。叶子节点、索引、根哈希和证明被发送到`checkMerkleTree`函数进行验证。结果如下所示：

```
//testCasedemo 的输出
{
   "0": "string: Cathy sent 8 tokens to David: true",
   "1": "string: Cathy sent 100000 tokens to David: false",
   "2": "string: David sent 7 tokens to Eric: true",
   "3": "string: David sent 100000 tokens to Eric: false"
}
```

输出显示未篡改的交易被验证为`true`，而篡改的交易被验证为`false`。

##### Plasma MVP 的交易默克尔树

在上一个代码示例中，我们展示了一个标准的默克尔树，叶子节点中记录了简单的交易消息“用户 A 向用户 B 发送了 x 个代币”。尽管我们能够证明这些交易消息可以通过记录在根链上的根哈希以及由用户或操作员提交的证明来验证，但一个简单的消息格式是不够的。



