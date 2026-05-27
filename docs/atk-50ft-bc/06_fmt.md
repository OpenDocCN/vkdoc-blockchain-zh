# 五、比特币挖矿如何集中化

## Chapter 5: How Bitcoin mining centralised

### 火灾陷阱时代
The firetrap era

比特币承诺任何人都可以自己开采比特币——你可以从无到有(除了电力和硬件)制造神奇的互联网货币。采矿难度每 14 天自动调整一次，以保持大约每 10 分钟一次的阻塞率，早期的难度确实很低。

挖掘的工作原理是尽可能快地反复计算一个特定的函数。早在 2009 年，人们就已经意识到显卡会更加高效^([^(128)](index_split_022.html#note_128 "128"))——图形处理单元(GPU)旨在快速运行简单的计算来计算视频游戏像素，同类处理能够以普通 CPU 的 800 倍速度计算比特币散列。到 2010 年，这已经成为正常的采矿方法。这些是消费显卡，所以只要几百美元，任何人都可以开矿，而且在价格处于第一次泡沫的上升趋势时，提前出柜是非常可行的。(特别是如果你偷了电，这是一种流行的策略。)

Mining works by calculating one specific function over and over, as absolutely fast as possible. As far back as 2009, people had realised that graphics cards would be much more efficient^([^(128)](index_split_022.html#note_128 "128")) – a graphics processing unit (GPU) is designed to run simple calculations very fast to compute video game pixels, and the same sort of processing was able to compute Bitcoin hashes eight hundred times as fast as a general CPU. By 2010, this had become the normal mining method. These were consumer graphics cards, so mining was still accessible to anyone with a few hundred dollars, and it was quite feasible to come out ahead while the price was on the upward slope of the first bubble. (Particularly if you stole the electricity, a popular strategy.)

![Image](img/image-2.jpeg)

这种东西是比特币矿工自豪地拍下来展示给大家看的。来源:kill 仓鼠，Buttcoin 基金会；原始来源不明。

The sort of thing home Bitcoin miners proudly photographed to show everyone back in the day. Source: Killhamster, Buttcoin Foundation; original source unknown.

这些日子里有许多令人捧腹又令人毛骨悚然的故事。现已关闭的比特币采矿事故博客展示了家庭矿工自豪的照片，这些照片展示了他们丑陋不堪的 firetrap 采矿钻机。 ^([^(129)](index_split_022.html#note_129 "129")) 这个著名的故事发布于 2011 年 6 月:

我受够了比特币。这钱很容易赚，但不值得(字面上的)热。

> 我的卧室里有 4 台配有多个超频 5850 的机器
>
> 风扇速度为 100%
>
> 房间很温暖，但还过得去
>
> 有一天天气突然变热
>
> 在我睡觉时严重中暑
>
> 被带到急诊室，浑身裹着冰袋，喝大量的佳得乐和水
>
> 似乎过了很久之后，终于冷静下来
>
> 发现我现在有轻微的永久性脑损伤，因为我的大脑发热并肿胀了很多

我希望我是在开玩笑。^([^(130)](index_split_022.html#note_130 "130"))

进一步提高效率是可能的。2012 年底，Butterfly Labs 发布了使用现场可编程门阵列(FPGA)的采矿硬件，这是一种可以对电路进行编程的硅芯片。这是当时显卡效率的五倍(每千瓦时哈希数)。这是工业比特币挖掘的开始，也是终端用户挖掘的衰落。

比特币挖矿在 2013 年用专用集成电路(ASICs)完全产业化。这些几乎是 FPGAs，但作为定制的硅芯片制造，效率也高得多。最大的比特币矿商现在赞助开发新的专用集成电路供自己使用——从 2013 年开始，如果不设计自己的采矿芯片，你就无法竞争。

你可以购买 ASIC 采矿钻机——2017 年 5 月，比特大陆 AntMiner S9 在 1323 瓦^([^(131)](index_split_022.html#note_131 "131"))下以 13.5 万亿哈希/秒的价格为 1161 美元——但它们将很快过时，除非你有非常便宜或免费的电力，否则你不太可能盈利。

(我认识一个人，他在家里采矿一直持续到 2014 年，密切关注电力和硬件成本，当家庭采矿不再可行时，即使有 ASICs 也停止了。他赢了几百美元，在有乐趣的时候尽情享受。然而，这不是通常的故事。)

从 2014 年开始，矿业网络几乎完全基于中国，依靠非常便宜的补贴本地电力运行 ASICs。(长期以来一直有人猜测，这其中很大一部分是为了逃避货币管制——用人民币购买电力，用比特币兑换美元。^([^(132)](index_split_022.html#note_132 "132")))2017 年 6 月 30 日，比特币网络总哈希速率为每秒 5.5 万亿次——这是截至 2011 年初 GPU 时代哈希速率的 5.5×10 ^(18) 或三百万倍。

关于采矿的一切都是批量更有效率。截至 2016 年底，75%的比特币哈希表是在*一栋建筑*中产生的，使用 140 兆瓦^([^(133)](index_split_022.html#note_133 "133"))——或者超过当时谷歌全球所有*数据中心估计用电量的一半。^([^(134)](index_split_022.html#note_134 "134"))*

偶尔有人呼吁通过改变散列函数来使采矿重新民主化；其他一些加密货币故意选择在显卡或 ASIC 上效率不高的哈希函数。但是情况总是这样，任何函数，尤其是像散列这样简单的函数，在专用于该函数的硬件上比在更通用的硬件上更有效。我们知道如何将哈希函数编程到 FPGA 中进行挖掘，然后基于它构建 ASIC。如果比特币的哈希值发生变化，新的专用集成电路将紧随其后，只需要制造周期。

There have been occasional calls to re-democratise mining by changing the hash function; some other cryptocurrencies deliberately chose hash functions that wouldn’t be efficient on a graphics card or an ASIC. But it is always the case that *any* function, particularly a simple one like a hash, will be more efficient on hardware specialised to just that function than on more general-purpose hardware. And we know how to program a hash function into an FPGA for mining and then base an ASIC on it. If the Bitcoin hash were to change, new ASICs would follow with only manufacturing lead time.

### 为了乐趣和利益滥用你的权力
Abusing your hashpower for fun and profit

比特币依赖于分布式共识:区块链就是大多数矿商所说的那样。共识模型依赖于这样一个事实，即你不能随随便便地超越所有其他矿工——因此它不是“由数学决定的”，而是由经济学决定的，在多个参与者之间保持平衡。

不幸的是，比特币生态系统中的每一股力量都倾向于集中化。采矿受益于规模经济，因此它已经从 PC 上的采矿发展到显卡、可编程芯片(FPGAs)和 ASICs。

中本聪最初的比特币白皮书假设了一个任何人都可以加入的对等网络。在实践中，矿工们运营着自己的中央通信池，以前是比特币中继网络，现在被称为快速互联网比特币中继引擎(FIBRE)，因为它更高效。

(这在 2016 年 1 月几乎成为单点故障，因为 BRN 即将因缺乏资金而关闭，分散的点对点网络将无法处理流量。)

截至 2017 年 3 月，三个池控制了超过 50%的哈希率，六个池控制了超过 75%的哈希率，最大的单个池为 21.3%。^([^(135)](index_split_022.html#note_135 "135"))没有理由说多个池不可能只有一个所有者。最大的矿池所有者已经组成了一个卡特尔。^([^(136)](index_split_022.html#note_136 "136"))

如果你控制了超过 50%的矿业电力，你可以执行“51%攻击”，这允许你写最长的区块链，然后将被网络的其余部分视为规范。您可以重复消费已确认的交易，或者拒绝任何您不认可的新交易。你可以拒绝其他矿工的区块。你不能花别人的比特币，但你可以阻止失主花。

即使你有少于 50%的机会，你仍然可以发动类似的攻击，成功的机会高于平均水平。从 25%的散列率开始，一个自私的矿工可以发动 51%风格的攻击，并期望获得比其他方式更大的利润。^([^(137)](index_split_022.html#note_137 "137"))

这不是假设—2014 年 6 月和 7 月，挖掘池 GHash.io 的散列率几次超过 50%。 ^([^(他们指责一名流氓雇员。)](index_split_022.html#note_138 "138")[](index_split_022.html#note_139 "139"))

比特币分散了不应该分散的东西，然后又集中起来，但浪费了。

Bitcoin decentralises things that should not be decentralised, then centralises them anyway but wastefully.