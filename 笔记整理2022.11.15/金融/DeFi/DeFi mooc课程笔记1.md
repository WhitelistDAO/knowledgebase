DeFi Mooc Lesson2
区块链的可扩展性
可扩展的方法
更快的共识
支付渠道：大多数交易为离线的点对点
两个节点完成签名后才进行结算
参与者只在渠道创建和关闭时候上链
双向渠道也是可能的
支付网络：多个双向的支付渠道
Layer2方法：打包多笔交易
以太坊Rollup扩容（SNARK隐私证明）
zkRollup
从或向第2层转移资产
ZKEVM
数据可用性
zkSync：将所有交易摘要存储在L1
zkPorter：将交易数据存储在一个新的链上
客户可以选择协调者如何存储其账户资产
Optimistic Rollup
协调者发送交易数据不需要证明
兼容EVM，协调者工作简单
需要等待验证者的挑战期间
侧链：去中心化程度和安全性低
跨链交互性
交互性：用户能够把资产和储备移动到另外一个区块链生态上
可组合性：使在某一区块链的Dapp能调动另一个区块链上的Dapp
方案
建造联邦链桥：需要外部校验者
通过轻节点：在两个兼容EVM的链上放两个轻节点