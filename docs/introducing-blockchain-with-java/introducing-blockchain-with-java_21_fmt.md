# 第 5 章 搭建网络与多线程

在重写`run()`方法之前，我们有一个`queue`字段，用于填充想要连接的对等节点的端口号。如第 19-21 行所示，我们通过构造函数将一些预定端口号填入`queue`。之前提到过，我们将使用本地端口作为对等节点。这意味着我们不需要更改 IP 地址来连接不同的对等节点，而是使用本地 IP 地址`127.0.0.1`，通过更改连接的本地端口来实现。

## 练习 5-2

尝试运用已学知识创建一个数据库表来存储端口列表，而不是在此处硬编码端口号。

加分项：尝试使用`ECoin`类中的`init()`方法以编程方式完成所有设置，而不是手动通过 SQLite 浏览器操作。

我们继续看下一段代码，这里将探索该线程的`run()`方法：

```java
@Override
public void run() {
    while (true) {
        try (Socket socket = new Socket("127.0.0.1", queue.peek())) {
            System.out.println("正在端口 " + queue.peek() + "发送区块链对象");
            queue.add(queue.poll());
            socket.setSoTimeout(5000);

            ObjectOutputStream objectOutput =
                new ObjectOutputStream(
                    socket.getOutputStream());
            ObjectInputStream objectInput =
                new ObjectInputStream(
                    socket.getInputStream());

            LinkedList<Block> blockChain = BlockchainData
                .getInstance().getCurrentBlockChain();
            objectOutput.writeObject(blockChain);

            LinkedList<Block> returnedBlockchain =
                (LinkedList<Block>) objectInput
                    .readObject();
            System.out.println(" 返回的 BC 账本 ID = " +
                returnedBlockchain.getLast()
                    .getLedgerId() +
                " 大小= " + returnedBlockchain.getLast()
                    .getTransactionLedger().size());

            BlockchainData.getInstance()
                .getBlockchainConsensus(
                    returnedBlockchain);

            Thread.sleep(2000);

        } catch (SocketTimeoutException e) {
            System.out.println("套接字超时");
            queue.add(queue.poll());
        } catch (IOException e) {
            System.out.println("客户端错误: " +
                e.getMessage() + " -- 端口错误: " +
                queue.peek());
            queue.add(queue.poll());
        } catch (InterruptedException |
                ClassNotFoundException e) {
            e.printStackTrace();
            queue.add(queue.poll());
        }
    }
}
```

由于我们需要持续联系其他对等节点并共享区块链，因此像挖矿线程一样，我们将此线程置于连续的`while`循环中。首先来看我们的 try-with-resources 结构。我们从实例化一个套接字开始，使用本地 IP 和`queue`中的第一个端口号。接着在控制台打印一条消息以记录操作，然后在第 29 行将刚刚用于实例化套接字的端口号移动到队列末尾。下一行我们设置了套接字超时。在第 32 和 33 行，我们实例化了`ObjectOutputStream`和`ObjectInputStream`对象。我们将主要使用这些对象提供的方法来发送区块链，然后接收响应的区块链。第 35 行我们使用服务层方法获取区块链对象，下一行使用`objectOutput`将区块链对象发送给对等节点。此时，我们发送的区块链将由对等节点检查并与本地区块链进行比较。当该过程完成后，对等节点将根据其共识方法返回胜出的区块链。我们在第 38 行使用`objectInput`检索此区块链。然而，由于我们不信任对等节点，接下来一行我们将其通过自己的共识算法处理。任何对等节点都可以修改本地机器上的应用程序来尝试发送虚假信息。这就是为什么我们要运行共识算法来验证之前收到的区块链，假设它来自良善节点。这样良善节点就能持续传播并扩展正确的区块链，同时阻止任何共享虚假区块链的尝试。

我们将在下一章介绍服务层时详细解释共识算法以及验证区块链所采取的步骤。第 40 行我们让线程休眠两秒，然后循环尝试连接下一个对等节点。最后，在这个方法中，查看我们的 catch 块也很重要。

到目前为止，只有成功连接到对等节点后，我们才会将端口号发送到队列末尾。然而，如果连接失败，我们也在 catch 块中包含了将当前端口号发送到队列末尾的语句。这确保我们不会卡在尝试连接单个对等节点的过程中。

**重要提醒！** 请记住，任何对等节点都不应信任其他节点。

### 5.3.2 PeerServer 线程

现在我们来研究响应其他对等节点请求的一端。`PeerServer`线程监听特定端口，接收其他对等节点`PeerClient`线程发来的客户端请求。在我们的实现中，将为每个传入请求创建一个独立的`PeerRequestThread`。这样我们就可以同时处理多个请求。让我们查看以下代码片段：

```java
package com.company.Threads;

import java.io.IOException;
import java.net.ServerSocket;

public class PeerServer extends Thread {

    private ServerSocket serverSocket;

    public PeerServer(Integer socketPort) throws IOException {
        this.serverSocket = new ServerSocket(socketPort);
    }

    @Override
    public void run() {
        while (true) {
            try {
                new PeerRequestThread(
                    serverSocket.accept()).start();
            } catch (IOException ex) {
                ex.printStackTrace();
            }
        }
    }
}
```

在第 10 到 12 行，我们声明了一个`ServerSocket`并设置构造函数来为服务器分配`socketPort`。这是服务器监听请求的端口。在`run()`方法内部，我们设置了一个连续循环，每当服务器套接字收到请求时，就创建一个新的`PeerRequestThread`来处理该请求。这样，服务器线程将始终可以处理新的传入请求。

## 5.4 PeerRequestThread