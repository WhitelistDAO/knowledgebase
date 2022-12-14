## DeFi聚合器
### 聚合器
#### 什么是聚合器
一个适用多种多样DeFi协议的聚合平台，在各个提供流动性挖矿的DeFi协议之间自动进行移仓，帮助用户获得更高的收益。简单来说，就是DeFi世界中的一个基金产品。只不过把中心化的投资团队给去掉，由代码写好的智能合约来代替行使传统基金投资理财的功能。
#### 聚合器好处
1. 聚合器控制用户与市场、Dapps、投资平台等的交互，提供详细的用户界面UI。聚合器使用户可以轻松选择市场上最有利的路径，从而降低gas成本；
2. 更好的定价：与单个 DEX 相比，DEX 聚合器可以提供更好的执行价格，帮助交易者以最高利率进行清算和去中心化交易所的用户因大滑点而遭遇的问题；
3. 提供流动性：DEX 聚合器为想要交易大量数字代币的交易者提供大量流动性。


![2022-12-07 152104.jpg](../_resources/2022-12-07 152104.jpg)

### 聚合器分类
#### 一：
1. 链外聚合器：(1inch，paraswap)
主流方式，能生成多个链，十分灵活，但可能存在操作员领先用户的情况。
2. 链上聚合器（swapswap）
进行原子性路径选择或者套利，但不太可能有效覆盖4个以上的DEX。
#### 二：
1. DEX 聚合器（1inch、Paraswap、Uniswap、Curve）有助于许多去中心化交易所的成本优化。
2. 借贷聚合器（Compound、Aave）从各种去中心化借贷协议中优化最佳借贷收益率。
3. 增产农业聚合器，利用所有产生收益的 Defi 协议（贷款、流动性提供和清算），收益聚合器（Yearn Finance）提供优化的策略。


![2022-12-07 152332.jpg](../_resources/2022-12-07 152332.jpg)

4. 跨链聚合器，通过各种跨链桥接协议，跨链聚合器优化跨链交换。
### 收益聚合器
#### Yield Farming
  Yield farming是 DeFi 的一个领域，它允许加密货币投资者通过将他们的代币转移到产生收益的智能合约来获得回报。在这个过程中，投资人是流动性提供者（LP），流动性池是一个充满现金的智能合约。
  除了在借贷时获得利息奖励和在提供流动性时获得费用奖励外，LP开始获得治理代币奖励，以增加他们对平台早期采用的参与度。利息、费用和代币分配形式的各种奖励为 DeFi 收益聚合器奠定了基础。
#### Yield Aggregator 
  收益聚合器，也称为“自动复合器”或“收益优化器”，通过结合不同的 DeFi 协议（智能合约）和策略来最大化投资者的利润，在收益经济中发挥着至关重要的作用。 
  收益聚合器是一组智能合约，汇集投资者的加密资产（代币），并通过预编程和自动执行的策略将其投资于收益支付产品和服务的组合。
#### How does it work
收益耕作过程通常期望参与者锁定或抵押他们的资金，收益聚合器通过自动化耕作过程来产生尽可能高的收益。
农场是种植作物以产生产量的地方。此概念也适用于 DeFi 中的yield farming，其中农民（加密货币投资者）将他们的投资（作物）用于产生利润（收益）。收益聚合器结合各种农民的投资，以促进使用不同策略赚取利润，同时保持闲置并等待积累被动收入，因为收益聚合器提供的自动化服务为他们做了这一切。 


![2022-12-07 152754.jpg](../_resources/2022-12-07 152754.jpg)



在 **Phase 0**，资金聚集在智能合约内。一个资金池通常只包含一种资产。用户将资产存入资金池中，获得代表他们在资金池中所占份额的代币作为回报。
在 **Phase 1**，资金池中的资产被质押到 Compound、Aave 或 Maker 等借贷平台上借入另一种资产。这一阶段不是必要的，可以跳过。这一步的主要目的是使用另一种资产（而非池中原有的资产）来执行流动性挖矿策略。
在**Phase 2** 涉及复杂程度不同的收益策略。在这一阶段，人们提供的不是非收益型资产就是收益型资产。久而久之，收益型产生收益并大幅增加。
在最后阶段 Phase 3，Phase 2 产生的收益被拿到公开市场上出售，换成资金池中原有的资产，然后重新进入 Phase 0，并通过 Phase 1 和 Phase 2 重新部署
