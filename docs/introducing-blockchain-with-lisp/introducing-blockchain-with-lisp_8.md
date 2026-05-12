# 4. 扩展区块链

![../images/510363_1_En_4_Chapter/510363_1_En_4_Figa_HTML.jpg](img/510363_1_En_4_Figa_HTML.jpg)

*扩展，作者：D. Bozhinovski*

在上一章中，我们实现了区块链的基本组件。在本章中，我们将通过智能合约和点对点支持来扩展区块链。



## 4.1 智能合约实现

比特币的区块链是可编程的，这意味着用户可以对交易条件本身进行编程。例如，用户可以编写*脚本*（简短的代码片段）来添加完成交易前必须满足的要求。

在 2.4 节中，我们创建了一个可执行文件并发送给朋友，但他们无法更改该可执行文件，因为他们没有原始代码。即使他们有原始代码，也并非所有用户都具备编程技能。

智能合约的意义在于，允许非编程人员在不修改原始代码的情况下，调整交易流程的行为。

### 4.1.1 文件 `smart-contracts.rkt`

其理念是实现一种用户可以使用的小型语言。我们的实现将依赖于交易：

```racket
1   (require "transaction.rkt")
```

我们需要扩展原始的 `valid-transaction?`，使其在计算有效性时也考虑合约：

```racket
1   (define (valid-transaction-contract? t c)
2     (and (eval-contract t c)
3          (valid-transaction? t)))
```

现在，我们将实现一个过程，它接收一个交易、一个合约、一种脚本语言（实际上只是一个 S-表达式），并返回某个值。返回的值可以是 true、false、数字或字符串。

```racket
1   (define (eval-contract t c)
2     (match c
3       [(? number? x) x]
4       [(? string? x) x]
5       [`() #t]
6       [`true #t]
7       [`false #f]
8       [`(if ,co ,tr ,fa) (if co tr fa)]
9       [`(+ ,l ,r) (+ l r)]
10      [else #f]))
```

我们在这里使用了新语法，叫做 `match`。它类似于 `cond`，区别在于它可以直接比较对象的结构。例如，`? <expr> <pat>` 在 `<expr>` 为 true 时匹配成功，并将值存储在 `<pat>` 中。在上面的代码中，如果我们传入一个数字，它会返回这个数字本身。此外，如果我们传入值 `true`（即 `c` 匹配 `true`），它会返回 `#t`。另一个例子是，如果 `c` 匹配形如 `(if X Y Z)` 的结构（已引用^(⁸)），那么它将返回 `(if X Y Z)` 的求值结果。

以下是一些使用示例：

```racket
1   > (define test-transaction (transaction "BoroS" "Boro" "You" "a book"
2     '() '()))
3   > (eval-contract test-transaction 123)
4   123
5   > (eval-contract test-transaction "Hi")
6   "Hi"
7   > (eval-contract test-transaction '())
8   #t
9   > (eval-contract test-transaction 'true)
10  #t
11  > (eval-contract test-transaction 'false)
12  #f
13  > (eval-contract test-transaction '(if #t "Hi" "Hey"))
14  "Hi"
15  > (eval-contract test-transaction '(if #f "Hi" "Hey"))
16  "Hey"
17  > (eval-contract test-transaction '(+ 1 2))
18  3
```

然而，我们还没有在语言中使用任何交易的值。让我们用更多的命令来扩展它：

```racket
1   ...
2        [`from (transaction-from t)]
3        [`to (transaction-to t)]
4        [`value (transaction-value t)]
5   ...
```

现在我们可以这样做：

```racket
1   > (eval-contract test-transaction 'from)
2   "Boro"
3   > (eval-contract test-transaction 'to)
4   "You"
5   > (eval-contract test-transaction 'value)
6   "a book"
```

我们将实现更多运算符，使脚本语言更具表达力：

```racket
1   ...
2        [`(* ,l ,r) (* l r)]
3        [`(- ,l ,r) (- l r)]
4        [`(= ,l ,r) (equal? l r)]
5        [`(> ,l ,r) (> l r)]
6        [`(< ,l ,r) (< l r)]
7        [`(and ,l ,r) (and l r)]
8        [`(or ,l ,r) (or l r)]
9   ...
```

然而，该语言实现存在一个问题。考虑对 `(+ 1 2)` 和 `(+ (+ 1 2) 3)` 的求值：

```racket
1   > (eval-contract test-transaction '(+ 1 2))
2   3
3   > (eval-contract test-transaction '(+ (+ 1 2) 3))
4   . . +: contract violation
```

问题出在匹配子句 `` [`(+ ,l ,r) (+ l r)]`` 上。当我们匹配 `'(+ (+ 1 2) 3))` 时，最终得到 `(+ '(+ 1 2) 3)`，而 Racket 无法将带引号的列表与数字相加。这个问题的解决方案是*递归地*求值每个子表达式。因此匹配从 ``[`(+ ,l ,r) (+ l r)]`` 变为 ``[`(+ ,l ,r) (+ (eval-contract t l) (eval-contract t r))]``。

在这种情况下，求值过程如下：

```racket
1   (eval-contract t '(+ (+ 1 2) 3))
2   = (eval-contract t (list '+ (eval-contract t '(+ 1 2))
3                               (eval-contract t 3)))
4   = (eval-contract t (list '+ (+ 1 2) 3))
5   = (eval-contract t (list '+ 3 3))
6   = (eval-contract t '(+ 3 3))
7   = (eval-contract t 6)
8   = 6
```

记住带引号列表和不带引号列表之间的区别很重要；后者会尝试求值。在这个例子中，我们巧妙地运用了引号来产生所需的结果。

我们需要重写所有运算符：

```racket
1   ...
2        [`(+ ,l ,r) (+ (eval-contract t l) (eval-contract t r))]
3        [`(* ,l ,r) (* (eval-contract t l) (eval-contract t r))]
4        [`(- ,l ,r) (- (eval-contract t l) (eval-contract t r))]
5        [`(= ,l ,r) (= (eval-contract t l) (eval-contract t r))]
6        [`(> ,l ,r) (> (eval-contract t l) (eval-contract t r))]
7        [`(< ,l ,r) (< (eval-contract t l) (eval-contract t r))]
8        [`(and ,l ,r) (and (eval-contract t l) (eval-contract t r))]
9        [`(or ,l ,r) (or (eval-contract t l) (eval-contract t r))]
10   ...
```

语言中的 `if` 实现也存在同样的问题。所以我们也需要修改它：

```racket
1   ...
2        [`(if ,co ,tr ,fa) (if (eval-contract t co)
3                           (eval-contract t tr)
4                           (eval-contract t fa))]
5   ...
```

因此，最终的过程变为：

```racket
1   (define (eval-contract t c)
2     (match c
3       [(? number? x) x]
4       [(? string? x) x]
5       [`() #t]
6       [`true #t]
7       [`false #f]
8       [`(if ,co ,tr ,fa) (if (eval-contract t co)
9                              (eval-contract t tr)
10                             (eval-contract t fa))]
11      [`(+ ,l ,r) (+ (eval-contract t l) (eval-contract t r))]
12      [`from (transaction-from t)]
13      [`to (transaction-to t)]
14      [`value (transaction-value t)]
15      [`(+ ,l ,r) (+ (eval-contract t l) (eval-contract t r))]
16      [`(* ,l ,r) (* (eval-contract t l) (eval-contract t r))]
17      [`(- ,l ,r) (- (eval-contract t l) (eval-contract t r))]
18      [`(= ,l ,r) (= (eval-contract t l) (eval-contract t r))]
19      [`(> ,l ,r) (> (eval-contract t l) (eval-contract t r))]
20      [`(< ,l ,r) (< (eval-contract t l) (eval-contract t r))]
21      [`(and ,l ,r) (and (eval-contract t l) (eval-contract t r))]
22      [`(or ,l ,r) (or (eval-contract t l) (eval-contract t r))]
23      [else #f]))
```

现在用户可以提供脚本代码，例如 `(if (= (+ 1 2) 3) from to)`：

```racket
1   > (eval-contract test-transaction '(if (= (+ 1 2) 3) from to))
2   "Boro"
3   > (eval-contract test-transaction '(if (= (+ 1 2) 4) from to))
4   "You"
```

最后，我们提供输出，即交易的有效性检查：

```racket
1   (provide valid-transaction-contract?)
```


好的，作为一名高级文档工程师和翻译员，我将严格按照您提供的注意事项和示例，将给定的英文文本翻译成中文。


### 4.1.2 更新现有代码

现在我们已经实现了智能合约的逻辑，接下来需要解决的是前端问题——用户如何使用其功能。为此，我们将更新实现，使其支持从文件中读取合约。如果存在名为 `contract.script` 的文件，我们将使用 `read` 读取并解析它，然后运行代码。

我们将重写 `blockchain.rkt` 中的货币发送过程，使其接受合约。该过程与之前相同，只是我们使用 `valid-transaction-contract?` 代替了 `valid-transaction?`。

```
1   (define (send-money-blockchain b from to value c)
2     (letrec ([my-ts
3               (filter (lambda (t) (equal? from (transaction-io-owner t)))
4                       (blockchain-utxo b))]
5               [t (make-transaction from to value my-ts)])
6       (if (transaction? t)
7           (let ([processed-transaction (process-transaction t)])
8             (if (and
9                  (>= (balance-wallet-blockchain b from) value)
10                  (valid-transaction-contract? processed-transaction c))
11                  (add-transaction-to-blockchain b processed-transaction)
12                  b))
13           (add-transaction-to-blockchain b '()))))
```

接下来，我们将更新 `utils.rkt`，添加这个用于读取合约的辅助过程：

```
1   (define (file->contract file)
2     (with-handlers ([exn:fail? (lambda (exn) '())])
3       (read (open-input-file file))))
```

这里，我们使用了 `with-handlers`，它接受一个过程来处理可能出错的情况——在本例中，是 `read` 或 `open-input-file` 出错的情况。

最后，确保将 `file->contract` 添加到 `utils.rkt` 的 `provide` 列表中。此外，在 `main.rkt` 中，更新所有对 `send-money-blockchain` 的调用，额外传入 `(file->contract "contract.script")` 作为参数，以便处理从 `contract.script` 文件读取的合约。

我们还需要更新 `blockchain.rkt` 和 `main.rkt`，因为它们现在依赖于智能合约包中实现的过程。我们将在这两个文件中添加 `(require "smart-contracts.rkt")`。

![../images/510363_1_En_4_Chapter/510363_1_En_4_Figb_HTML.gif](img/510363_1_En_4_Figb_HTML.gif)**练习 4-1**

想出几个有效的表达式，并使用 `eval-contract` 对其进行求值。

![../images/510363_1_En_4_Chapter/510363_1_En_4_Figc_HTML.gif](img/510363_1_En_4_Figc_HTML.gif)**练习 4-2**

重复上一个练习，但这次使用 `contract.script` 文件。

**提示**：此练习可能需要您创建一个可执行文件。

## 4.2 点对点实现

在第 3.6.2 节中，我们使用 DrRacket 来执行区块链实现。这在测试时是可以的。但是，如果我们想与其他用户共享该实现并让他们执行它，就会有点不方便，因为无法在不同用户之间共享数据。

在本节中，我们将实现点对点支持，以便对我们实现感兴趣的用户可以加入系统/社区。

在我们深入实现之前，图 4-1 展示了我们将要构建的架构的高级概览。

![../images/510363_1_En_4_Chapter/510363_1_En_4_Fig1_HTML.jpg](img/510363_1_En_4_Fig1_HTML.jpg)

**图 4-1** 点对点架构

每个对等节点（连接到系统的用户）的列表将由以下部分组成：

-   **对等节点上下文数据**：例如与其他对等节点的关系、已连接的对等节点列表等信息。
-   **通用处理器**：转换对等节点上下文数据。

此外，将有两种方式与其他对等节点建立通信：

-   一个对等节点将接受来自其他对等节点的新连接。
-   一个对等节点将尝试连接/建立与其他对等节点的新连接。

每当建立连接时，对等节点将通过通用处理器相互通信，解析和评估诸如同步/更新区块链、更新对等节点列表等命令。

考虑这个示例场景：假设有三个对等节点——节点 1、节点 2 和节点 3。节点 1 和节点 2 在线，节点 3 当前离线。节点 1 拥有以下有效对等节点列表：(节点 1, 节点 2, 节点 3)。节点 2 的对等节点列表为空。根据图示，节点 1 将接受新连接并尝试连接其他对等节点。因此，节点 1 将尝试连接节点 2。此连接将成功，下一步是节点 1 向节点 2 发送一些数据（例如，有效对等节点列表）。

节点 2 的对等节点列表之前是空的，但现在将与节点 1 的列表合并，因此它将变为 (节点 1, 节点 2, 节点 3)。节点 1 和节点 2 相互连接，并且它们将持续尝试连接节点 3。一旦节点 3 变为可用，将执行相同的算法，节点 3 将加入网络。

通过这种方法，目标是构建一个类似于图 1-1 和图 1-3（第 1 章）中高层描述的系统。

构建这种类型的通信系统自然很复杂。建议您查阅 Racket 手册（按 F1 键）以了解将要使用的每个过程。

### 4.2.1 `peer-to-peer.rkt` 文件

首先，我们将为区块实现添加依赖项，并依靠序列化来向其他对等节点发送数据：

```
1   (require "blockchain.rkt")
2   (require "block.rkt")
3   (require racket/serialize)
```

#### 4.2.1.1 对等节点上下文结构

我们将实现保存对等节点信息的结构体，以便拥有一个用于将数据发送到正确目标的引用。`peer-info` 结构体包含一个对等节点的 IP 地址和端口。可以将 IP 地址和端口类比为街道地址和门牌号。

```
1   (struct peer-info
2     (ip port)
3     #:prefab)
```

`peer-info-io` 结构体额外包含了用于在对等节点之间发送和接收数据的 IO 端口（可以看作是通信通道）：

```
1   (struct peer-info-io
2     (peer-info input-port output-port)
3     #:prefab)
```

我们将 `peer-info` 和 `peer-info-io` 分开的原因是，稍后在 `main-p2p.rkt` 中，我们可能没有输入/输出端口的上下文（在连接到对等节点之前），这样分离可以让我们灵活地重用结构体。

最后，`peer-context-data` 包含单个对等节点所需的所有信息，即：

-   有效对等节点列表
-   已连接的对等节点列表
-   区块链的引用

```
1   (struct peer-context-data
2     (name
3      port
4      [valid-peers #:mutable]
5      [connected-peers #:mutable]
6      [blockchain #:mutable])
7     #:prefab)
```

有效对等节点列表将根据从已连接的对等节点检索到的信息进行更新。已连接的对等节点列表是 `valid-peers` 的一个（不一定严格的）子集。区块链将使用从所有节点组合的数据进行更新。我们将这些字段设置为可变，因为这提供了一种简单的数据更新方式。



#### 4.2.1.2 通用处理器

通用处理器将是一个`handler`过程，它将被服务器（图中“接受新连接”部分）和客户端（图中“连接到新对等节点”部分）共同使用。它将是一个接受命令（与智能合约的`eval-contract`实现性质类似的命令）并根据命令执行相应操作的过程。

以下是对等节点之间相互发送的命令列表：

| **请求** | **响应** | **说明** |
| --- | --- | --- |
| `get-valid-peers` | `valid-peers:X` | 对等节点可以请求有效节点列表。响应为 X（有效节点列表）。注意此响应应自动触发`valid-peers`命令。 |
| `get-latest-blockchain` | `latest-blockchain:X` | 对等节点可以向另一个节点请求最新区块链。响应为 X（区块链最新版本）。这应触发`latest-blockchain`命令。 |
| `latest-blockchain:X` | | 当对等节点收到此请求时，如果区块链有效，则会更新它。 |
| `valid-peers:X` | | 当对等节点收到此请求时，会更新有效节点列表。 |

此表中的命令允许对等节点之间同步数据。现在我们将提供处理器的实现。它接受一个`peer-context`和输入/输出端口。基于这些参数，它将读取输入（命令）并向对等节点发送相应的输出（求值后的命令）：

```
1   (define (handler peer-context in out)
2     (flush-output out)
3     (define line (read-line in))
4     (when (string? line) ; it can be eof
5       (cond [(string-prefix? line "get-valid-peers")
6              (fprintf out "valid-peers:~a\n"
7                       (serialize
8                        (set->list
9                         (peer-context-data-valid-peers peer-context))))
10              (handler peer-context in out)]
11             [(string-prefix? line "get-latest-blockchain")
12              (fprintf out "latest-blockchain:")
13              (write
14               (serialize (peer-context-data-blockchain peer-context)) out)
15              (handler peer-context in out)]
16             [(string-prefix? line "latest-blockchain:")
17              (begin (maybe-update-blockchain peer-context line)
18                     (handler peer-context in out))]
19             [(string-prefix? line "valid-peers:")
20              (begin (maybe-update-valid-peers peer-context line)
21                     (handler peer-context in out))]
22             [(string-prefix? line "exit")
23              (fprintf out "bye\n")]
24             [else (handler peer-context in out)])))
```

这里我们使用了一些新过程：

*   输出缓冲区（与对等节点的输出通信通道）通常填充了字节。每次发送消息时，我们需要刷新（清空）此缓冲区，以避免重发之前的消息。我们通过`flush-output`实现这一点。
*   `read-line`类似于`read`，不同之处在于它一旦遇到换行符就会停止读取。
*   `string-prefix?`用于检查一个字符串是否以另一个字符串开头。
*   `fprintf`类似于`printf`，不同之处在于我们可以额外提供第一个参数来指定消息的发送目标。
*   `set->list`将集合转换为列表。

在`latest-blockchain`的 cases 中有一个小技巧——我们使用了`write`而不是`(fprintf out "latest-blockchain:~a\n")`。原因是`print`（以及`printf`和`fprintf`）不能可靠地用于需要以特定格式输出的内容。例如，`print`会打印带引号的字符串（使打印的数据对用户更易读），这在我们尝试反序列化接收到的数据时会造成混乱，因此我们希望以“原始”格式发送数据。

下一步是实现更新区块链和有效节点列表的过程，条件是区块链有效且其工作量大于我们当前的工作量。

```
1   (define (maybe-update-blockchain peer-context line)
2     (let ([latest-blockchain
3            (trim-helper line #rx"(latest-blockchain:|[\r\n]+)")]
4           [current-blockchain
5            (peer-context-data-blockchain peer-context)])
6       (when (and (valid-blockchain? latest-blockchain)
7                   (> (get-blockchain-effort latest-blockchain)
8                      (get-blockchain-effort current-blockchain)))
9         (printf "Blockchain updated for peer ~a\n"
10                 (peer-context-data-name peer-context))
11         (set-peer-context-data-blockchain! peer-context
12                                            latest-blockchain))))
```

我们首次使用了`#rx"..."`——这用于指定一个正则表达式。可以将其视为一种在字符串中定义搜索模式的方法。

例如，`#rx"(latest-blockchain:|[\r\n]+)"`匹配以下字符串：

*   `latest-blockchain:a\n`
*   `latest-blockchain:b\n`
*   一般形式：`latest-blockchain:...\n`

上述过程仅在区块链有效且工作量高于当前区块链时才会更新区块链。我们将工作量定义为所有区块的`nonce`之和：

```
1   (define (get-blockchain-effort b)
2     (foldl + 0 (map block-nonce (blockchain-blocks b))))
```

更新有效节点列表就是将当前有效节点列表与新接收的列表合并，从而改变`peer-context`结构：

```
1   (define (maybe-update-valid-peers peer-context line)
2     (let ([valid-peers (list->set
3                         (trim-helper line #rx"(valid-peers:|[\r\n]+)"))]
4           [current-valid-peers (peer-context-data-valid-peers
5                                 peer-context)])
6       (set-peer-context-data-valid-peers!
7        peer-context
8        (set-union current-valid-peers valid-peers))))
```

我们还使用了一个辅助过程，它会从字符串中移除命令（前缀），使我们能够专注于输入。例如，当我们收到`valid-peers:X`时，它会移除`valid-peers:`，从而让我们轻松提取出`X`。

```
1   (define (trim-helper line x)
2     (deserialize
3      (read
4       (open-input-string
5        (string-replace line x "")))))
```

至此，`handler`的实现部分结束。现在有了一个可被对等节点用于接受命令并更新节点列表和区块链的过程。在下一节中，我们将实现对等节点之间的通信——它们应使用我们实现的这些命令相互通信。



#### 4.2.1.3 服务器端实现

当一个节点连接到另一个节点（服务器）时，应当执行以下流程：

1.  服务器应等待传入节点发送某个命令。
2.  服务器应使用 `handler` 过程来处理必要的数据。
3.  服务器应将处理后的数据发送回传入节点。

然而，如果连接了多个节点，该过程将发生“阻塞”，即第二个节点必须等待第一个节点被处理，第三个节点必须等待第二个节点，依此类推。

为了解决这个问题，我们采用线程。`accept-and-handle` 是为主传入节点服务的主要过程。该过程接受一个连接（监听器对象）和一个节点上下文，并为每个传入连接在线程中启动 `handler`：

```
1   (define (accept-and-handle listener peer-context)
2     (define-values (in out) (tcp-accept listener))
3     (thread
4      (lambda ()
5        (handler peer-context in out)
6        (close-input-port in)
7        (close-output-port out))))
```

我们使用了一个名为 `tcp-accept` 的新过程，它接受一个连接并返回输入端口（用于读取数据）和输出端口（用于发送数据）。通过 `define-values` 语法，我们存储了这两个值。

`peers/serve` 是主要的服务器监听器。这部分直接复制自 Racket 文档，有兴趣的读者可以查阅文档了解更多实现细节。简而言之，*custodian*（监管者）是一种容器，它能确保内存中没有虚假的线程或输入/输出端口，并为我们处理这一切。

```
1   (define (peers/serve peer-context)
2     (define main-cust (make-custodian))
3     (parameterize ([current-custodian main-cust])
4       (define listener
5         (tcp-listen (peer-context-data-port peer-context) 5 #t))
6       (define (loop)
7         (accept-and-handle listener peer-context)
8         (loop))
9       (thread loop))
10     (lambda ()
11       (custodian-shutdown-all main-cust)))
```

`tcp-listen` 过程会持续监听特定端口，等待新的传入连接。

#### 4.2.1.4 客户端实现

接下来，我们将实现 `connect-and-handle`——一个尝试连接其他节点的过程，而我们之前构建的是为传入节点服务的过程。这个过程与 `accept-and-handle` 类似，但功能上是相反的，因为它不接受新连接，而是尝试建立新连接：

```
1   (define (connect-and-handle peer-context peer)
2     (begin
3       (define-values (in out)
4         (tcp-connect (peer-info-ip peer)
5                      (peer-info-port peer)))
7       (define current-peer-io (peer-info-io peer in out))
9       (set-peer-context-data-connected-peers!
10        peer-context
11        (cons current-peer-io
12              (peer-context-data-connected-peers peer-context)))
14        (thread
15         (lambda ()
16           (handler peer-context in out)
17           (close-input-port in)
18           (close-output-port out)
20           (set-peer-context-data-connected-peers!
21            peer-context
22            (set-remove
23             (peer-context-data-connected-peers peer-context)
24             current-peer-io))))))
```

这个过程比较长，因此需要进行一些解析：

1.  `tcp-connect` 过程尝试连接到特定的 IP 地址和端口（我们从 `peer-info` 结构中提取这些值）。
2.  连接成功后，`tcp-connect` 会返回输入和输出端口，我们可以分别用它们来读取数据和写入数据。
3.  接着，当前上下文的已连接节点列表将被更新。
4.  最后，我们启动一个线程，使用 `handler` 来处理通信。连接结束时，我们进行清理，并将该节点从节点列表中移除。

下一个过程是确保我们与所有已知节点保持连接。我们再次使用线程，原因与服务器端相同——我们不希望这个过程在尝试连接一个节点的过程中阻塞程序，使其无法连接到其他客户端。这个逻辑与 `peers/serve` 形成对应关系，`tcp-connect` 则与 `tcp-accept` 形成对应关系。我们使用 `sleep` 在处理前等待几秒钟，以使过程具有更高的性能。

```
1   (define (peers/connect peer-context)
2     (define main-cust (make-custodian))
3     (parameterize ([current-custodian main-cust])
4       (define (loop)
5         (let ([potential-peers (get-potential-peers peer-context)])
6           (for ([peer potential-peers])
7             (with-handlers ([exn:fail? (lambda (x) #t)])
8               (connect-and-handle peer-context peer))))
9         (sleep 10)
10         (loop))
11       (thread loop))
12     (lambda ()
13       (custodian-shutdown-all main-cust)))
```

为了实现 `get-potential-peers`，我们首先从节点上下文中获取已连接且有效的节点列表。那些不在已连接节点列表中的有效节点，就是我们可以建立新连接的潜在节点。

```
1   (define (get-potential-peers peer-context)
2     (let ([current-connected-peers
3            (list->set
4             (map peer-info-io-peer-info
5                  (peer-context-data-connected-peers peer-context)))]
6           [valid-peers (peer-context-data-valid-peers peer-context)])
7       (set-subtract valid-peers current-connected-peers)))
```

#### 4.2.1.5 整合各部分

接下来的过程将向所有节点（无论是连接到我们的还是我们连接的）发送 ping，以尝试同步区块链数据并更新其他内容，例如有效节点列表：

```
1   (define (peers/sync-data peer-context)
2     (define (loop)
3       (sleep 10)
4       (for [(p (peer-context-data-connected-peers peer-context))]
5         (let ([in (peer-info-io-input-port p)]
6               [out (peer-info-io-output-port p)])
7           (fprintf out "get-latest-blockchain\nget-valid-peers\n")
8           (flush-output out)))
9       (printf "Peer ~a reports ~a valid and ~a connected peers.\n"
10               (peer-context-data-name peer-context)
11               (set-count
12                (peer-context-data-valid-peers peer-context))
13               (set-count
14                (peer-context-data-connected-peers peer-context)))
15       (loop))
16     (define t (thread loop))
17     (lambda ()
18       (kill-thread t)))
```

以下过程是入口点，所有功能在此一并启动：

```
1   (define (run-peer peer-context)
2     (begin
3       (peers/serve peer-context)
4       (peers/connect peer-context)
5       (peers/sync-data peer-context)))
```

最后，我们导出必要的对象：

```
1   (provide (struct-out peer-context-data)
2             (struct-out peer-info)
3             run-peer)
```

### 4.2.2 更新现有代码

我们需要修改 `main-helper.rkt` 以包含点对点实现：

```
1   ; ...
2   (require "peer-to-peer.rkt")
3   ; ...
5   (provide (all-from-out "blockchain.rkt")
6             (all-from-out "utils.rkt")
7             (all-from-out "peer-to-peer.rkt")
8             format-transaction print-block print-blockchain print-wallets)
```


好的，作为高级文档工程师和翻译员，我将严格遵循您的注意事项和示例格式，将给定的英文文本翻译成中文。


### 4.2.3 `main-p2p.rkt` 文件

这里我们将把所有组件整合在一起并使用它们。我们希望这个实现能够接受一些输入参数，例如区块链数据库文件以及每个节点的 IP 和端口地址。

一旦我们创建了可执行文件，就可以通过使用命令行参数将输入传递给它。例如，如果我们的可执行文件名为 `blockchain`，我们可以通过运行 `./blockchain <param1> <param2> <...>` 来向其传递额外的数据。Racket 提供了一个名为 `current-command-line-arguments` 的内置过程，它会将这些参数读入一个向量（类似于列表），然后我们使用 `vector->list` 将其转换为列表以供进一步处理。

```
1   (require "main-helper.rkt")

3   (define args (vector->list  (current-command-line-arguments)))

5   (when (not (= 3 (length args)))
6     (begin
7       (printf "Usage: main-p2p.rkt db.data port ip1:port1,ip2:port2...")
8       (newline)
9       (exit)))
```

`string-to-peer-info` 是一个辅助过程，用于对节点信息进行额外的解析：

```
1   (define (string-to-peer-info s)
2     (let ([s (string-split s ":")])
3       (peer-info (car s) (string->number (cadr s)))))
```

接下来，我们解析参数：

```
1   (define db-filename (car args))
2   (define port (string->number (cadr args)))
3   (define valid-peers
4     (map string-to-peer-info (string-split (caddr args) ",")))
```

然后，我们使用 `file-exists?` 检查数据库文件是否存在。如果存在，该文件将包含来自上一个区块链的内容。如果文件不存在，我们将继续创建一个新文件。

```
1   (define db-blockchain
2     (if (file-exists? db-filename)
3         (file->struct db-filename)
4         (initialize-new-blockchain)))
```

我们提供了创建新区块链的功能：

```
1   (define wallet-a (make-wallet))

3   (define (initialize-new-blockchain)
4     (begin
5       (define coin-base (make-wallet))

7       (printf "Making genesis transaction...\n")
8       (define genesis-t (make-transaction coin-base wallet-a 100 '()))

10       (define utxo (list
11                      (make-transaction-io 100 wallet-a)))

13       (printf "Mining genesis block...\n")
14       (define b (init-blockchain genesis-t "1337cafe" utxo))
15       b))
```

接下来是初始化当前节点的代码——它被命名为 `Test peer`，并包含来自解析后的命令行参数的数据（端口、有效节点等）。

```
1   (define peer-context
2     (peer-context-data "Test peer"
3                        port
4                        (list->set valid-peers)
5                        '()
6                        db-blockchain))
7   (define (get-blockchain) (peer-context-data-blockchain peer-context))

9   (run-peer peer-context)
```

我们持续导出数据库，以便在用户退出应用时拥有最新的信息。

```
1   (define (export-loop)
2     (begin
3       (sleep 10)
4       (struct->file (get-blockchain) db-filename)
5       (printf "Exported blockchain to '~a'...\n" db-filename)
6       (export-loop)))

8   (thread export-loop)
```

最后，我们创建一个过程来持续挖掘空区块。请注意，点对点实现在线程模式下运行，因此如果我们持续运行此过程，不会发生阻塞。

```
1   (define (mine-loop)
2     (let ([newer-blockchain
3            (send-money-blockchain (get-blockchain)
4                                   wallet-a
5                                   wallet-a
6                                   1
7                                   (file->contract "contract.script"))])
8       (set-peer-context-data-blockchain! peer-context newer-blockchain)
9       (printf "Mined a block!")
10       (sleep 5)
11       (mine-loop)))

13   (mine-loop)
```

我们可以继续创建一个可执行文件并与我们的朋友分享。如果我们知道他们的 IP 地址（或者他们知道我们的），我们就可以建立连接，从而形成一个系统。

## 4.3 总结

恭喜！在本章中，我们为区块链实现添加了两个重要的新特性：智能合约和点对点支持。至此，本书的区块链实现部分已结束。

脚注 1 2 索引 A 抽象 抽象语法树 非对称算法 非对称密钥算法 B 比特币 区块链 定义 加密与解密 `blockchain.data` 文件 `blockchain.rkt` 文件 结构 Hashcash 算法 哈希与验证 初始化 奖励 交易 添加 验证 C 中心化场所 命令行参数 组合 条件过程 构造 加密工厂 `current-command-line-arguments` D 数据结构 去中心化账本 解密 `define-macro` 语法 `define-syntax-rules` 反序列化 数字签名 双花问题 DrRacket E 加密与解密 非对称密钥算法 函数 对称密钥算法 可执行文件 F 函数 G 通用处理器 H, I, J, K Hashcash 算法 哈希 高阶过程 卫生宏 L Lambda 语言 账本 `letrec` 语法 Lisp 抽象 抽象语法树 组合 数据结构 实现 语言原语/公理 过程 符号表达式 语法 `list` 循环 宏 M, N, O 宏 `main-helper.rkt` 文件 `main.rkt` 文件 挖矿 可变性 P, Q 包 `peer-context` 结构 节点上下文数据 `peer-info` 结构 `peer-info-io` 结构 点对点实现 架构 `main-p2p.rkt` 文件 `peer-to-peer.rkt` 文件 客户端实现 通用处理器 节点上下文结构 服务端实现 更新现有代码 点对点网络 原始类型 过程 处理交易 工作量证明 公钥密码学 R Racket 添加定义 计算 条件过程 配置与安装 `construct` `crypto` 工厂 点对 求值 可执行文件 高阶过程 `list` 可变性 包 原始类型 过程 过程与函数 引号 递归过程 作用域 结构 线程 递归函数 递归过程 RSA S 作用域 序列化 S-表达式 SHA 智能合约 智能合约实现 `smart-contracts.rkt` 文件 更新现有代码 `string-to-peer-info` `struct-out` 语法 结构 对称密钥算法 T 线程 交易实现 `transaction-io.rkt` 文件 `transaction.rkt` 文件 `transaction-io` 结构 `transaction-io.rkt` 文件 `transaction.rkt` 文件 数字签名 处理交易 树 信任 U 未花费的交易输出（UTXO） `utils.rkt` 文件 V 变量遮蔽 W, X, Y, Z `wallet.rkt` 文件 空白 工作流程
