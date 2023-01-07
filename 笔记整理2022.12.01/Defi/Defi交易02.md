- # Defi交易02

  - ## 简介

  - V2 was launched in March 2020 and it was a huge improvement of V1 that allowed direct swaps between any ERC20 tokens, as well as chained swaps between any pairs.

  - ## 白皮书学习

    - [去中心化交易所：Uniswap v2白皮书中文版 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/255190320)

    - ### 交易对

      - #### 理解：

      - 相比于V1版本只支持ERC20/ETH(注意创建交易池是使用的ETH，实际上交易池中的交易对是ERC20/WETH，这样说明是为了强调一种绑定关系)，而V2版本允许流动性提供商为任意的两个ERC20代币创建交易对

        - ##### 那为什么V1只支持ERC20/ETh pair呢？

          - 1.V1中有一个方便快捷的交易路由查询功能，我们只需要在pairs中查询是否存在两个ERC20代币的地址是否存在就可以知道该交易对是否存在。

          - 2.在V1中如果要使ERC20/ERC20存在，那么需要承受两次gas、即两次手续费（或者说两次滑点损失[(109条消息) 深入分析AMM恒定乘积模型的滑点与无常损失_知道创宇区块链安全实验室的博客-CSDN博客_恒定乘积](https://blog.csdn.net/SierraW/article/details/118543720)），因为我们使用A代币换ETH需要花费一次gas，而使用ETH换B代币又需要花费一次gas=>V2就是为了解决此问题！

        - ##### V2的解决了两次gas吗？如何解决？

          - 答案是肯定的，在V2版本中，需要创建交易对的A代币和B代币会在pairs中生成一个固定的地址，那么就可以很方便的查询到该交易对是否存在

          - 比如在pairs中，A代币和B代币存在一个流动性交易池，B代币和C代币存在一个流动性交易池，我们如果知道这两个交易池的地址信息，我们既可以很容易的创建A代币和C代币的流动性交易池

    - ### Price Oracle(抗风险的价格预言机)

      - [什么是区块链预言机（BlockChain Oracle） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/52369816)=>本质是一种中间件

      - 有一种比较极端的说法：任何能影响价格的因素，都存在套利和风险=>任何与价格有关的地方，都需要Oracle；在V1版本中，在一段时间内抬高ETH价格从而可以存在套利风险或者说使套利成功的安全案例是存在的，所以这种说法是有理可循的。[【文章阅读】抵押品不足的贷款攻击 - 阿菜ACai - 博客园 (cnblogs.com)](https://www.cnblogs.com/ACaiGarden/p/16073115.html)

      - V2的Price Oracle避免操纵攻击采取了一种Oracle延迟更新机制TWAP
        - 原理：简单来说，就是用均价替代预言机的实时报价，通过市场的均衡价格的机制来使得攻击失效或者说削弱攻击；这里题外话，我觉得市场是一种抽象的真实的概念，是一种集体意识的表现，操纵市场的波动也终究取决于人，而如果有少部分人妄想摧毁这种集体意识那么市场本身也应该采取相应的保护措施(股市的熔断机制)。

    - ### Flash Swap(闪电交换)

      - 功能同闪电贷异曲同工，无需抵押品，就可以借出uniswap中的流动性代币，只要在规定时间内完成后，将代币归还就可以完成一笔闪电兑换，同样的如果规定时间内没有归还，那么就会执行原子操作（回滚），使得借贷失效，借出的代币归还到流动池当中

      - 在代码层面上，我的理解是在回调函数中拿到钱，然后执行回调函数中执行自己的事务，最后加上把钱还回去的逻辑。

  - ## 合约代码（主要）（solidity）

    - 我看的解析

      - [剖析DeFi交易产品之Uniswap：V2上篇 | 登链社区 | 区块链技术社区 (learnblockchain.cn)](https://learnblockchain.cn/article/2824)

      - [剖析DeFi交易产品之Uniswap：V2中篇 | 登链社区 | 区块链技术社区 (learnblockchain.cn)](https://learnblockchain.cn/article/3047)

      - [剖析DeFi交易产品之Uniswap：V2下篇 | 登链社区 | 区块链技术社区 (learnblockchain.cn)](https://learnblockchain.cn/article/3100)

    - 拉代码自己解析[Uniswap Labs (github.com)](https://github.com/uniswap)

    - 依旧是仿写[Programming DeFi: Uniswap V2. Part 1 - Going the distance (jeiwan.net)](https://jeiwan.net/posts/programming-defi-uniswapv2-1/)

  -  