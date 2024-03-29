# DeFi 安全

## 一、DeFi 与 AMM 类型

1. AMMs 类型：

自动做市商（AMMs）是去中心化金融（DeFi）生态系统的一部分，它们允许数字资产使用流动资金池以无许可和自动化的方式进行交易。AMMs 用户为流动资金池提供加密代币，其价格由恒定的数学公式确定。AMMs 是一种数学函数，根据流动性池对资产进行算法定价。目前，有几个 AMM公式被利用来迎合不同的资产定价策略。一些比较流行的 AMM 公式如下：

- 恒定乘积做市商公式：x*y=k
  
  恒定乘积做市商公式绘制时，它是一条凸形曲线，其中 x 和 y 代表流动性池中两个代币的数量，而 k 代表乘积。该公式有助于根据每个代币的可用数量，为两个代币创造一个价格范围。为了保持 k 不变，当 x 的供应增加时，y 的供应必须减少，反之亦然。因此，所产生的价格本质上是不稳定的，因为交易的规模可能会

影响到与池子规模有关的价格。大额交易造成的较高滑点可能会出现无常损失。

- 恒定和做市商公式：x+y=k
  
  恒定和做市商公式在绘制时形成一条直线。它是零滑点交易的理想模型，但不幸的是，它不能提供无限的流动性。这个模型是有缺陷的，因为当报价与其他地方交易的资产的市场价格不同时，它就会出现套利机会。套利者可以耗尽流动性池中的全部储备，不再为其他交易者留下可用的流动性。这种模式不适合大多数AMM 使用案例。

- 恒定平均值做市商公式：v=(ΠtBt)Wt
  
  恒定平均值做市商公式，或者也被称为”价值函数”，是由 Balancer 创造并使之流行的。它允许流动性池中有两个以上的代币，并允许池子以超出标准 50/50分布的比例添加不同的代币。这允许对池子里的不同资产进行可变的暴露，并使流动性池子里的任何资产之间的交换成为可能。

- 稳定交换公式：
  
  该公式是恒定乘积与恒定和公式的混合体。它是由 Curve Finance 推广的。当投资组合相对平衡时，交易发生在恒定和曲线上，而当不平衡时则切换到恒定乘积曲线上。这允许较低的滑点和无常损失，但只适用于具有类似价值的资产，因为所需交易范围的价格总是接近于 1。例如，这对稳定币（DAI 和 USDC）和包装资产（wBTC 和 sBTC）之间的交易会很有用。大多数自动做市商（AMMs）面临三个主要挑战：高费用，高滑点和无常损失。

## 二、Uniswap

在去中心化金融(DeFi)的世界中，去中心化交易所(DEX)为最重要且最核心的一环。其中最具代表性的为 Uniswap，以一行简洁且优美的数学式作为核心模型，建造出一個全自动的交易市场。接下来我学习了一些关于 Uniswap 的基础功能。

- 1.1 流动资金池（Liquidity Pool）

流动资金池是锁定在智能合约中的加密货币，用于促进去中心交易所（DEX）上资产之间的交易。

流动资金池是一种机制，通过这种机制，用户可以将其资产汇集到 DEX 的智能合约中，为交易者提供资产流动性，以便在货币之间进行交换。流动性池为 DeFi 生态系统提供了急需的流动性，速度和便利性。AMM通过创建流动性池并为流动性提供者提供向这些池提供资产的激励来解决流动性有限的问题，池中的资产越多，池中的流动性越强，在去中心化交易所进行交易就越容易。

- 1.2 流动性提供者（LP，Liquidity Provider）

在加密生态系统中，流动性提供者（LP）是将代币存入流动资金池的用户。作为提供流动性的回报，用户通常会获得代表用户拥有的流动性池份额的流动性代币。这些用户通常被激励持有流动性代币，以获得一定比例的交易费用和其他加密奖励。

- 1.4 流动性代币（LP Token，Liquidity Provider Token）

流动性代币是创建并给予将资产存入流动性池的用户的代币。流动性代币代表流动性提供者拥有的流动资金池的份额，LP 通常获得一定比例的流动资金池交易费用作为财务激励。流动性代币通常可以转让，交换和抵押。流动性提供者仍然完全控制其抵押的代币，这些代币仅借给平台的协议。当流动性提供者想要收回流动性时，除了交易费用或贷款利息的任何累积佣金外，他们还必须烧毁流动性代币以接收其原始加密资产。流动性代币还允许自动做市商（AMM）

是非托管的，这意味着您仍然控制着您的资产，并可以随时赎回它们。

Uniswap：Uniswap 流动性提供者将获得同质化 ERC-20 流动性代币的奖励，这使得代币可以在更广泛的以太坊 DeFi 生态系统中使用。因此，即使通常没有直接市场购买和交易流动性代币本身，像 Uniswap 这样的流动性代币也可以用作 MakerDAO 等借贷协议的抵押品。值得注意的是，Uniswap 流动性与 UNI 治理代币不同，UNI 治理代币用于对新提案和其他形式的去中心化决策进行投票。

- 1.5 滑点（Slippage）

滑点是指交易的预期价格与执行交易的实际价格之间的差额。滑点通常发生在投资者在流动性差和交易量低的平台上购买或出售资产时。如果交易所订单簿上的买卖价格之间存在很大差距，资产购买者最终可能会为资产支付更多费用，或者在交易执行后获得的资产少于预期。
