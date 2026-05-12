# 应用将很快获得普及。所有这些用例都有一个共同且关键的元素：代币。下面，我们将描述如何使用代币来表示资产以资助项目，以及如何使用智能合约来编程代币。

© Weijia Zhang and Tej Anand 2022

W. Zhang and T. Anand, *Blockchain and Ethereum Smart Contract Solution Development*,
<https://doi.org/10.1007/978-1-4842-8164-2_10>

## 第十章 资助项目：代币与 Gas 费

#### 用于资助生态系统项目的代币

##### ICO 与 DeFi 中的代币

以太坊中的 ICO 是一种在 2017 年左右流行的筹资机制。它由 `ERC20` 代币实现，该代币使得代币在以太坊生态系统中可编程、可分发和可交易。`ERC20` 代币是同质化的，这意味着它仅具有价值，且彼此之间无法区分。`ERC20` 代币遵循位于以下 URL 的 `EIP-20` 规范：<https://eips.ethereum.org/EIPS/eip-20>

在 `ERC20` 规范中，定义了几个标准函数以及两个事件，所有发行 `ERC20` 代币的智能合约都需要相应地实现它们。函数规范如下：

```
// 返回代币的名称
function name() public view returns (string)

// 返回代币的符号。通常是几个大写字母，可选。
function symbol() public view returns (string)

// 返回代币使用的小数位数。
function decimals() public view returns (uint8)

// 返回代币的总供应量
function totalSupply() public view returns (uint256)

// 返回地址为 _owner 的账户余额。
function balanceOf(address _owner) public view returns (uint256 balance)

// 将 _value 数量的代币转移到地址 _to，并且必须触发 Transfer 事件。
function transfer(address _to, uint256 _value) public returns (bool success)

// 将 _value 数量的代币从地址 _from 转移到地址 _to，并且必须触发 Transfer 事件。
function transferFrom(address _from, address _to, uint256 _value) public returns (bool success)

// 允许 _spender 多次从你的账户提款，最高可达 _value 数量。如果再次调用此函数，它将用 _value 覆盖当前的 allowance。
function approve(address _spender, uint256 _value) public returns (bool success)

// 返回 _spender 仍然被允许从 _owner 提取的金额。
function allowance(address _owner, address _spender) public view returns (uint256 remaining)
```

**事件**

```
// 此事件必须在代币被转移时触发，包括零值转移
event Transfer(address indexed _from, address indexed _to, uint256 _value)

// 此事件必须在任何对 approve(address _spender, uint256 _value) 的成功调用时触发
event Approval(address indexed _owner, address indexed _spender, uint256 _value)
```

有两个已经为 `EIP-20` 标准实现的智能合约包：`OpenZeppelin` 包和 `ConsenSys` 包。开发者可以扩展这些包，并通过几行自定义代码创建他们自己的 `ERC20` 代币。例如，通过导入 `OpenZeppelin` 的 `ERC20` 包，开发者可以创建一个名为 `DEVELOPER_TOKEN`、符号为 `DEV`，并带有可自定义的总供应量作为构造函数输入的 `ERC20` 代币：

```
// contracts/DEVToken.sol
// SPDX-License-Identifier: MIT
pragma solidity ⁰.8.0;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract DEVToken is ERC20 {
    constructor(uint256 initialSupply) ERC20("DEVELOPER_TOKEN", "DEV") {
        _mint(msg.sender, initialSupply);
    }
}
```

当上述智能合约以指定的 `initialSupply` 部署时，部署的代币的总供应量 (`totalSupply`) 将等于 `initialSupply`。一旦创建了 `ERC20` 代币，就可以编写另一个智能合约。



##### 代币铸造与分发

为了处理代币的铸造与分发，该智能合约在用于 ICO 时有时也被称为众售智能合约。众售智能合约通常包含以下功能：

- **代币与以太币的比率** – 以太币与目标代币之间的兑换比率。
- **众售时间** – 代币可供分发的开始与结束时间。
- **KYC/AML** – 众售可以设置一个白名单，只有白名单中的发送者才能参与众售。
- **退款** – 智能合约还可以实现代币退款功能。

除了众筹，`ERC20` 代币也广泛应用于其他去中心化金融（DeFi）项目，例如 Compound（借贷）、Uniswap（交易所）和 USDC（稳定币）。

在以太坊社区中，关于 `ERC20` 代币是功能型代币还是证券型代币也存在一些讨论。这些问题因国家或地区而异，应咨询法律专业人士。

##### NFT 中的代币

与 `ERC20` 代币不同，非同质化代币（NFT）是可区分的，可用于代表所有权。例如，出生证明、文凭和租赁合同都是非同质化的，具有明确的所有权。

NFT 被定义为 `EIP-721` 标准，用于代表：
- 实物资产，如房屋、汽车和艺术品
- 虚拟收藏品，如数字艺术和收藏卡
- “负资产”，如贷款和债务

`EIP-721` 的详细规范位于以下网址：[`eips.ethereum.org/EIPS/eip-721`](https://eips.ethereum.org/EIPS/eip-721)

与 `ERC20` 代币类似，`ERC721` NFT 也定义了代币名称、代币符号和总供应量。但 NFT 有以下一些主要区别：
- 每个 NFT 代币都有一个唯一的索引。
- 每个 NFT 代币都有一个所有者。
- 由于 NFT 可以指向区块链外部的实物或虚拟资产，因此存在一个 `ERC721Metadata` 接口，该接口定义了一个名为 `tokenURL` 的函数。
```
function tokenURI(uint256 _tokenId) external view returns (string);
```
此 `tokenURL` 函数接收 `_tokenId` 作为输入，并返回一个指向传统数字系统中定义的 NFT 项目的统一资源标识符（URI）。
- 每个 NFT 代币可以通过以下函数从一个所有者转移到另一个所有者：
```
function transferFrom(address _from, address _to, uint256 _tokenId) external payable;
```
- 还有其他函数或接口帮助 NFT 代币进行分配、转移或识别。

基于 `EIP-721`/`ERC721` 标准的 NFT 代币已被多个项目实现。例如，0xcert 和 OpenZeppelin 已经实现了 `ERC721` 代币智能合约包。开发者可以轻松地扩展 `ERC721` 包，创建自己的 `ERC721` 非同质化代币。

例如，为了给学生的文凭创建 NFT，可以使用以下示例代码编写智能合约：

```
// SPDX-License-Identifier: MIT
pragma solidity ⁰.7.0;

import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/release-v3.4/contracts/token/ERC721/ERC721.sol";

contract TTCDiploma is ERC721 {
    uint private _tokenIds;
    address admin;

    constructor() ERC721("TexasTechnologyCollegeDiploma", "TTC") public {
        admin = msg.sender;
    }

    function issueDiploma(address student, string memory tokenURI) public returns (uint256) {
        require(msg.sender == admin); // 只有管理员才能颁发文凭。
        _tokenIds++;
        uint256 newDiplomaId = _tokenIds;
        _mint(student, newDiplomaId);
        _setTokenURI(newDiplomaId, tokenURI);
        return newDiplomaId;
    }
}
```

在这个程序中，编写了 `TTCDiploma` 智能合约用于为学生颁发文凭。部署智能合约时，提供了代币名称和代币符号。同时，智能合约的部署地址被指定为管理员地址。只有管理员才能执行特权操作，例如颁发文凭。在 `issueDiploma` 函数中，



`the sender`被检查是否具有管理员权限。如果有，则递增`tokenId`，并生成一个新的文凭 ID。然后，这个新的`diplomaId`被分配一个`tokenURI`，该 URI 指向一个外部源，用于检索该 ID 对应的文凭。当学生想要检索文凭时，他们只需签署一条消息并将其发送到`diplomaId`指定的 URI。由该 URI 指向的文凭服务器将检查已签名的消息，以验证请求者是该文凭的合法所有者，然后将文凭输出给请求者。

