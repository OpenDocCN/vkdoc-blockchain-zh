# `PeerRequestThread` 类

`PeerRequestThread` 处理到达我们对等节点的每个特定请求。让我们查看以下代码片段并解释其工作原理：

```java
package com.company.Threads;

import com.company.Model.Block;
import com.company.ServiceData.BlockchainData;

import java.io.IOException;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.net.Socket;
import java.util.LinkedList;

public class PeerRequestThread extends Thread {

    private Socket socket;

    public PeerRequestThread(Socket socket) {
        this.socket = socket;
    }

    @Override
    public void run() {
        try {
            ObjectOutputStream objectOutput = new ObjectOutputStream(socket.getOutputStream());
            ObjectInputStream objectInput = new ObjectInputStream(socket.getInputStream());

            LinkedList<Block> recievedBC = (LinkedList<Block>) objectInput.readObject();
            System.out.println("LedgerId = " + recievedBC.getLast().getLedgerId() +
                    " Size= " + recievedBC.getLast().getTransactionLedger().size());
            objectOutput.writeObject(BlockchainData.getInstance().getBlockchainConsensus(recievedBC));
        } catch (IOException | ClassNotFoundException ex) {
            ex.printStackTrace();
        }
    }
}
```

我们需要声明一个套接字和一个构造函数，该构造函数将在创建此线程时从`PeerServer`类接受一个套接字。这个套接字是到发送请求的对等节点的链接。`run()`方法很简单；在第 26 和 27 行，我们使用从`PeerServer`接收的套接字实例化`ObjectOutputStream`和`ObjectInputStream`对象。回想一下，在`PeerClient`类中，我们在请求中发送一个`LinkedList<Block>`对象，其中包含该对等节点的区块链。在第 29 行，我们使用`objectInput`类实例化发送的区块链并将其命名为`receivedBC`。第 30 和 31 行只是从`receivedBC`记录一些信息。最后，在第 32 行，我们使用`objectOutput`在通过区块链共识算法运行`receivedBC`后发回获胜的区块链。你会注意到，我们从服务层调用的方法与我们在`PeerClient`类中收到响应时调用的方法是相同的。换句话说，在验证传入的区块链并在该链与本地链之间达成共识时，无论它是作为请求发送还是作为响应接收，我们最终使用的算法都是相同的。

## 5.5 总结

在本章中，我们介绍了多个线程的创建，这些线程将使我们的应用程序能够响应迅速且不间断地运行。我们学习了如何使用套接字向其他对等节点发送和接收请求。我们了解了组成点对点网络所需的元素。我们学习了如何运行应用程序的用户界面，以及如何设置挖矿子程序。以下是我们目前涵盖概念的简要总结：

- 创建线程
- 使用线程处理需要并行运行的不同操作
- 创建和使用网络套接字
- 设置网络客户端和服务器线程
- 设计点对点网络
- 使用 JavaFX 应用程序线程

---

