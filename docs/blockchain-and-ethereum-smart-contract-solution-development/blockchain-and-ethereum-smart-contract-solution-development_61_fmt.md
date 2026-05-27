# 第 7 章 使用 Solidity 编写智能合约

## 获取消息函数

与 `setMessage` 函数类似，`getMessage` 函数也会创建一个智能合约对象，并在智能合约中调用 `retrieve` 函数。由于 `getMessage` 是一个不向区块链写入数据的函数，因此可以直接调用 `call()` 函数。此 `call()` 函数不需要 MetaMask 签署交易，也不消耗 gas 费用。

```javascript
function getMessage() {
    if (!ethEnabled()) {
        alert("Please install an Ethereum-compatible browser or extension like MetaMask to use this dApp!");
    }
    var myContract = new web3.eth.Contract(messagestorage_abi, messagestorage_contract.toLowerCase());
    var tx_getmessage = myContract.methods.retrieve().call(function(err, result) {
        document.getElementById("GetMessageValue").innerHTML = "message retrieved:" + result;
    })
}
```