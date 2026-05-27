# 8. Filecoin

发明并维护 IPFS 的同一家公司——协议实验室，创建了 Filecoin。它通过建立一个开源的去中心化存储网络扩展了 IPFS 的概念，并激励用户将数据固定到 IPFS。

Filecoin 的优越之处在于它提供了一种去中心化的存储解决方案，无需依赖大型的中心化存储方案。与纯基于 IPFS 的解决方案不同，Filecoin 节点有经济激励来保持活跃。在此示例中，我们将使用一个名为 `Lotus` 的 Filecoin 实现。它验证网络交易，管理 FIL 钱包，并可以执行存储和检索交易。

在本章结束时，您将能够执行以下操作：
- 创建一个 Filecoin 项目
- 配置 Truffle 以使用 Filecoin 端点
- 启动本地 Filecoin 服务器
- 添加要保存在 Filecoin 上的图像

## 如何在 Filecoin 本地节点上保存文件

为了在 Filecoin 中保存文件，你将学习如何从零开始创建一个项目并配置 Truffle，使其指向正确的地址。你将启动本地端点，最后通过命令行将一张图片添加到 Filecoin。

### 创建项目

在终端中，点击“新建终端”。现在，创建一个新文件夹来启动你的项目。

```bash
$ mkdir create filecoin
```

### 配置 Truffle

创建 `truffle-config.js` 文件。

```bash
$ touch truffle-config.js
```

在该文件中，配置 IPFS 和 Filecoin lotus 服务器的文件设置。设置以下值：
- 将 IPFS 地址设置为 `http://127.0.0.1:5001`。
- 将 Filecoin 地址设置为 `http://localhost:7777/rpc/v0`。

稍后你将使用 Ganache Filecoin 来启动这些端点。

```javascript
module.exports = {
  environments: {
    development: {
      ipfs: {
        address: "http://127.0.0.1:5001",
      },
      filecoin: {
        address: "http://localhost:7777/rpc/v0",
        storageDealOptions: {
          epochPrice: "2500",
          duration: 518400,
        }
      },
    }
  }
};
```

### 添加要保存的图片

创建 `badge` 文件夹。

```bash
$ mkdir badge
```

进入 `badge` 根文件夹。

```bash
$ cd badge
```

下载你将用于保存到 Filecoin 的图片。你也可以在此文件夹中复制并粘贴一张现有图片。`curl` 命令用于通过 URL 语法传输数据。

```bash
$ curl https://planouhost.z15.web.core.windows.net/badge.png > badge-image.png
```

### 安装依赖项

使用 Filecoin 标签安装基础 Ganache 包。

```bash
$ npm install ganache@filecoin
```

安装 Filecoin 对等依赖包。

```bash
$ npm install @ganache/filecoin
```

使用 Ganache 启动 Lotus 和 IPFS 本地服务器。它还会创建 10 个开发账户，每个账户拥有 100 Filecoins (FIL)。

### 启动本地端点

在本地启动 Ganache Filecoin。

```bash
$ npx ganache filecoin
```

### 将文件保存到 Filecoin

打开一个新的终端，保存 `badge` 文件夹。

```bash
$ truffle preserve ./badge/ --filecoin
```

如果一切顺利，输出将如下所示：

```
Preserving target: ./badge/
===========================
Warning: multiple plugins found that output the label "ipfs-cid".
✓ Loading target...
✓ Reading directory ./badge/...
✓ Opening ./badge\badge-image.png...
✓ Preserving to IPFS...
✓ Connected to IPFS node at http://127.0.0.1:5001
✓ Uploading...
Root CID: QmNjGGACAMpm718WjeM7oN3WJ5ntXKgwurH4mMivU6JXoS
./badge-image.png: QmemrBxhPpg8Hw9sPpAUTWRq3kNAFhCPKUDhMnc5U9KptS
✓ Preserving to Filecoin...
✓ Connected to Filecoin node at http://localhost:7777/rpc/v0
✓ Retrieving miners...
✓ Proposing storage deal...
Deal CID: bafyreidrt2pvuh44umo7vzebnxnw46qzntneyojah6oym4ximokyzvwxgq
✓ Waiting for deal to finish...
Deal State: StorageDealActive
```

现在，你的图片已保存在本地 Filecoin 节点中！

## 总结

在本章中，你学习了如何使用 Filecoin 保存文件。

在下一章中，你将学习什么是 ENS 以及如何使用它。