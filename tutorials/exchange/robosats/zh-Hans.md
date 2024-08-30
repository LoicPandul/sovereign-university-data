---
name: Robosats

description: 如何使用Robosats
---

![cover](assets/cover.webp)

RoboSats（https://learn.robosats.com/）是一种简单的方式，用于私下将比特币兑换为国家货币。它简化了点对点交易体验，并利用闪电网络保留发票来最小化保管和信任要求。

![video](https://youtu.be/XW_wzRz_BDI)

## 指南

> 本指南来自Bitocin Q&A（https://bitcoiner.guide/robosats/）。所有荣誉归他，支持他（https://bitcoiner.guide/contribute）；BitcoinQ&A也是一位比特币导师。联系他获取指导！

![image](assets/0.webp)

RoboSats - 一个简单且私密的基于闪电网络的P2P交易平台

## 在你开始之前

### 你需要知道的事情

| 术语         | 定义                                                                                                                                                                                         |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Robot        | 你自动生成的私密交易身份。不要重复使用同一个机器人，因为这可能会降低你的隐私。                                                                                                                  |
| Token        | 一串随机字符，用于生成你独特的机器人。                                                                                                                                                        |
| Maker        | 创建购买或出售比特币报价的用户。                                                                                                                                                              |
| Taker        | 接受另一个用户的购买或出售比特币报价的用户。                                                                                                                                                  |
| Bond         | 由双方用户锁定的一定数量的比特币，作为公平交易并完成交易部分的承诺。保证金通常是交易总额的3%，通过Hodl Invoices实现。                                                                          |
| Trade Escrow | 卖方用作持有交易金额比特币的方法，同样使用Hodl Invoices。                                                                                                                                      |
| Fees         | RoboSats收取交易金额的0.2%，分摊给maker和taker。Taker支付0.175%，maker支付0.025%。                                                                                                           |

## 你需要准备的东西

### 一个闪电钱包

RoboSats是闪电网络原生的，所以你需要一个闪电钱包来资助保证金，并作为买家接收购买的sats。由于RoboSats功能所使用的技术，选择钱包时应小心，因为并非所有钱包都兼容。

如果你是节点运行者，Zeus是迄今为止最好的选择。如果你没有自己的节点，我强烈推荐Phoenix，一个跨平台的移动钱包，设置简单，易于访问闪电网络。本指南中使用了Phoenix。

### 一些比特币

买家和卖家需要在任何交易发生之前资助一个保证金。这通常是一个非常小的金额（~交易金额的3%），但无论如何都是一个先决条件。

使用RoboSats购买你的第一批sats？为什么不让一个朋友借给你开始所需的小额金额呢！如果你是独自一人，这里还有一些其他获取一些无需KYC的sats的好方法，让你开始。

### 访问RoboSats

显然，你需要访问RoboSats！有四种主要方式可以做到这一点：

1. 通过Tor浏览器（推荐！）
2. 通过常规网络浏览器（不推荐！）
3. 通过Android APK
4. 你自己的客户端

如果你对Tor浏览器不熟悉，了解更多并在[这里](https://www.torproject.org/download/)下载。
对于希望通过Tor从手机访问RoboSats的iOS用户的快速提示。‘Onion Browser’不是Tor浏览器。请改用Orbot + Safari和Orbot + DuckDuckGo。

## 购买比特币

以下步骤是在2023年5月使用0.5.0版本通过Tor浏览器进行的。通过Android APK访问RoboSats的用户应该也是相同的步骤。

在撰写本文时，RoboSats仍在积极开发中，因此界面未来可能会有一些变化，但完成交易所需的基本步骤应该大体上保持不变。

> 当你首次加载RoboSats时，你会看到这个着陆页面。点击开始。

![image](assets/2.webp)

生成你的令牌并将其存储在安全的地方，如加密笔记应用或密码管理器。这个令牌可以用来在你的浏览器或应用在交易过程中意外关闭时恢复你的临时机器人ID。

![image](assets/3.webp)

遇见你的新机器人身份，然后点击继续。

![image](assets/4.webp)

点击Offers浏览订单簿。在页面顶部，你可以根据你的偏好进行筛选。确保注意债券百分比和平均汇率上的溢价。

- 选择购买
- 选择你的货币
- 选择你的支付方式

![image](assets/5.webp)

> 点击你想要接受的报价。输入你想从卖家那里购买的金额（以你选择的法定货币计），然后最后检查一下细节并点击接受订单。

如果卖家不在线（由其个人资料图片上的红点表示），你会看到一个警告，交易可能会比平常花费更长的时间。如果你继续进行，而卖家没有及时操作，你将获得他们债券金额50%的补偿，作为你浪费时间的补偿。

![image](assets/6.webp)

接下来，你需要通过支付屏幕上的发票来锁定你的交易债券。这是一个在你的钱包中冻结的保留发票。只有在你未能完成交易的一方时才会被收费。

![image](assets/7.webp)

在你的Lightning Wallet中，扫描二维码并支付发票。

![image](assets/8.webp)

接下来，在你的Lightning Wallet中为显示的金额生成一个发票，并粘贴到提供的空间中。

![image](assets/9.webp)

等待卖家锁定他们的交易金额。当这发生时，RoboSats将自动进入下一步，聊天窗口将打开。打个招呼并向卖家询问他们的法定货币支付信息。一旦提供，通过选择的方式发送支付，然后在RoboSats中确认。RoboSats中的所有聊天都是PGP加密的，这意味着只有你和你的交易伙伴可以阅读消息。

![image](assets/10.webp)

一旦卖家确认收到付款，RoboSats将自动使用之前提供的发票释放付款。

![image](assets/11.webp)

当发票被支付时，交易完成，你的债券被解锁。你将看到一个交易摘要。

![image](assets/12.webp)

检查你的Lightning Wallet，确认sats已经到账。

![image](assets/13.webp)

## 额外功能

除了显而易见的比特币买卖外，RoboSats还有一些其他你应该知道的功能。
Robot Garage
想要同时进行多笔交易，但不想在它们之间共享相同的身份？没问题！点击机器人标签页，生成一个额外的机器人并创建或接受你的下一个订单。
![image](assets/14.webp)

### 创建订单

除了接受别人的报价，你还可以创建自己的订单并等待另一个机器人来找你。

- 打开创建页面。
- 定义你的订单是购买还是出售比特币。
- 输入你想要买/卖的金额和货币。
- 输入你愿意使用的支付方式。
- 输入你愿意接受的市场溢价百分比。注意，这可以是一个负数，以低于当前市场价格的折扣出价。
- 点击创建订单。
- 支付闪电网络发票以锁定你的制造商保证金。
- 你的订单现在已经生效。坐下来等待有人接受它。

![image](assets/15.webp)

### 链上支付

RoboSats专注于闪电网络，但买家确实有选择将他们的sats接收到链上比特币地址的选项。买家锁定他们的保证金后可以选择此选项。选择链上后，买家将看到费用概览。此服务的额外费用包括：

- RoboSats收取的换币费 - 这个费用是动态的，取决于比特币网络的繁忙程度。
- 支付交易的挖矿费 - 买家可以配置这个费用。

![image](assets/16.webp)

### P2P 交换

RoboSats允许用户将sats换入或换出他们的闪电钱包。只需点击报价页面顶部的换币按钮即可查看当前的换币报价。

作为‘换入’报价的买家，你发送链上比特币给对方，并在扣除广告中的费用和/或溢价后，收到sats到你的闪电钱包。作为‘换出’报价的买家，你通过闪电网络发送sats，并在扣除任何费用和/或溢价后，收到比特币到你的链上地址。Samourai或Sparrow钱包用户也可以利用Stowaway功能完成交换。

RoboSats的换币报价还可以包括比特币的挂钩替代品，包括RBTC、LBTC和WBTC。如果与这些代币互动，你应该格外小心，因为它们都有各种权衡。挂钩比特币不是比特币！

![image](assets/17.webp)

### 运行你自己的RoboSats客户端

Umbrel、Citadel和Start9节点运行者可以直接在他们的节点上安装自己的RoboSats客户端。这样做的好处列举如下：

- 显著更快的加载时间。
- 更安全：你控制运行哪个RoboSats客户端应用。
- 从任何浏览器/设备安全访问RoboSats。如果你在本地网络上或使用VPN，无需使用TOR：你的节点后端处理所需的匿名化。
- 允许控制你连接到哪个P2P市场协调器（默认为robosats6tkf3eva7x2voqso3a5wcorsnw34jveyxfqi2fu7oyheasid.onion）

![image](assets/18.webp)

## 常见问题解答

### 我会被骗吗？
作为买家，如果您已经发送了交易所需的法币，但卖家未能向您释放sats，则您可以开启争议。如果在这个争议过程中，您能向RoboSats仲裁员证明您确实发送了法币，卖家的托管资金和他们的交易保证金将被释放给您。我该如何取消交易？

您可以在发布保证金后，通过点击交易菜单中的“协作取消”按钮来取消交易。如果您的交易对手同意取消，您将不会产生任何费用。但是，如果您的交易伙伴想要完成交易，而您仍然取消，您将失去您的交易保证金。

### RoboSats是否支持‘X’支付方式？

RoboSats对支付方式没有限制。如果您没有看到您想要的支付方式的任何报价，使用它创建您自己的报价！

![image](assets/19.webp)

### 当我使用RoboSats时，它会了解到我什么？

只要您通过Tor或Android应用使用RoboSats，就完全没有任何信息！在这里了解更多。

- Tor保护您的网络隐私。
- PGP加密保持您的交易聊天私密。
- 没有账户意味着每次交易一个机器人。这意味着RoboSats不能将多个交易关联到单一实体。

然而，有一些注意事项！作为发送者，闪电网络相当私密，但作为接收者则不是。如果您将资金接收到自己的闪电网络节点，您的节点ID将在您的发票中共享。这个节点ID为任何知道它的人提供了一个起点，尝试链接您的链上活动。如果用户选择通过链上支付接收他们的交易，也是如此。

为了缓解这一点，用户可以选择使用如闪电网络的代理钱包或链上的Coinjoin等解决方案。

### 联邦

目前，有一个由RoboSats开发团队操作的单一RoboSats协调器。在比特币中，任何形式的中心化都使得特定服务成为政府或监管机构可能不看好的更容易的目标。

鉴于RoboSats是一个开源项目，任何人都可以拿走代码并开始运行他们自己的协调器。虽然这在某种程度上将风险从单一目标分散开来，但它也分散了一个已经很少的流动性市场。

RoboSats团队意识到这一点，并已经开始着手于一个联邦模型。作为最终用户，这不应该太多地改变上面展示的交易流程，但是当出现不同的协调器时，您将有额外的视图或屏幕来添加或移除它们。

指南结束
https://bitcoiner.guide/robosats/