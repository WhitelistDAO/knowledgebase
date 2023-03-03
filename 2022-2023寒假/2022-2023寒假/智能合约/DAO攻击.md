## DAO攻击

#### DAO是什么

DAO，英文全称是Decentralized Autonomous Organization ，去中心化自治组织，是V神提出的一个概念。

The DAO则是区块链公司Slock.it发起的一个众筹项目。Slock.it是一家将区块链与物联网相结合的公司。一开始，他们只是想利用以太坊来开发他们的“全民分享网络”。随着开发的深入，他们发现，去中心分享经济很有前景。他们在伦敦的Devcon1大会上演示了这个概念和愿景，出乎意料的是，这个项目反响热烈。于是，就有了The DAO项目。在The DAO项目中，代币的持有人拥有很大的权力，比如项目资金的用途需要代币持有人投票决定，相关的提议由智能合约支撑。

创始人们野心勃勃，他们想创建一个真正的“DAO”，一个真正去中心化自治组织。于是THE DAO项目开始众筹并最终取得了超过1200万个以太币的巨大成功。

但是在同年的6.12，Tual宣布称发现他们的软件中存在“递归调用漏洞”问题，但是不会对DAO资金产生风险。

#### DAO攻击

6.17攻击开始黑客利用这个递归漏洞不断从The DAO的资金池中分离财产，随后利用第二个漏洞避免被分离的资产被销毁。（正常情况下The DAO的资产被分离后会被销毁，但是黑客在调用结束之前就将资转移到了其他账户使得资产无法被销毁）黑客通过这两个漏洞盗走了总计360万的以太坊，超过该项目筹集的总以太币数目的三分之一

最终的结果众所周知，经过激烈讨论以太坊决定实行硬分叉把所有的资金都退回去也不需要回滚，但是以太坊一分为二，变成原链（以太坊经典，ETC），新链（ETH）。

#### DAO攻击如何实现的

基础知识

1.跨合约调用

智能合约之间的调用本质上是外部调用，可以使用message call或者创建智能合约对象的形式进行调用。

（1）使用message call

比如合约1调用合约2的某个方法：

（2）还原智能合约对象

如果已知合约的地址，可以通过如下方式获取到合约对象：

2.智能合约发送ETH

我们可以在智能合约中用代码向某个地址（这个地址可以是人，也可以是智能合约）发送以太币，比较常见的两个方式是：

（1）调用send函数

（2）使用message call

这两个方式不同的是发送的gas数量，gas就是执行opcode需要花费的一种币，称作为gas也特别形象。当调用send方法时，只会发送一部分gas，准确地来讲，是2300gas，一旦gas耗尽就可能抛出异常。

而使用message call的时候，则是发送全部的gas，执行完之后剩余的gas会退还给发起调用的合约。

3.fallback函数

智能合约中可以有唯一的一个未命名函数，称为fallback函数。该函数不能有实参，不能返回任何值。如果其他函数都不能匹配给定的函数标识符，则执行fallback函数。

当合约接收到以太币但是不调用任何函数的时候，就会执行fallback函数。如果一个合约接收了以太币但是内部没有fallback函数，那么就会抛出异常，然后将以太币退还给发送方。

下面就是一个fallback函数的代码示例：

一般单纯使用message call或者send函数发送以太币给合约的时候，没有指明调用合约的某个方法，这种情况下就会调用合约的fallback函数。

攻击事件还原

我们先用简单的模拟代码来了解下整个攻击过程。

首先是存在漏洞的智能合约代码，Bank：

![img](https://z374q654zv.feishu.cn/space/api/box/stream/download/asynccode/?code=ODY1NGE1MjU2MWIxZDkyMTBlMDdjN2RlMzc3MWJkZmFfeUlYRmNDMUtNRURIMkJZd0p2RWxVcTZzMkg3dzlzb0tfVG9rZW46Ym94Y25mcmh3dDJhNnhJd3pORHAyZFJFNWtjXzE2NzU0MTU0MDA6MTY3NTQxOTAwMF9WNA)

用户可以通过addToBalance方法存入定量的以太币到这个智能合约，通过withdrawBalance方法可以提现以太币，通过getUserBalance方法可以获取到账户余额。

注意到这里是通过message call的方式来发送以太币，所以在调用sender的fallback函数的时候我们就会有充足的gas来进行循环调用。如果是send的方式，gas只有2300，稍微一操作就会耗尽gas抛出异常，是不够用来进行嵌套调用的。以下是不同操作所需要的gas数量：

![img](https://z374q654zv.feishu.cn/space/api/box/stream/download/asynccode/?code=MWM0NmI3NTNjMzBiM2IxYzJjZDBmNGFmZGFjZjhlNTZfcngyVDVOMm5HN1hvQkVMRE5RQmxERHBZOTdld0VtTmdfVG9rZW46Ym94Y25xbTNlelRON25yRGh4emdDR1ZQWnVnXzE2NzU0MTU0MDA6MTY3NTQxOTAwMF9WNA)

出问题的是withdrawBalance方法，特别是在修改保存在区块链的balances的代码是放在了发送以太币之后。

攻击代码如下：

![img](https://z374q654zv.feishu.cn/space/api/box/stream/download/asynccode/?code=YWRjNmMzNDQ4NDU4ZWI2MjJiYzcwZDJmZDgyMGQ3MTdfT0ozaE1JZlQ2SlR3QXdRWkF2SThVYmtwdE10MHZDWnJfVG9rZW46Ym94Y25lNGlOaFdwMUtZeTRVbjFBQXdOZ2lnXzE2NzU0MTU0MDA6MTY3NTQxOTAwMF9WNA)

这里的deposit函数是往Bank合约中发送10wei。withdraw是通过调用Bank合约的withdrawBalance函数把以太币提取出来。注意看这里的fallback函数，这里循环调用了两次Bank合约的withdrawBalance方法。

攻击的过程如下：

（1）假设Bank合约中有100wei，攻击者Attack合约中有10wei

（2）Attack合约先调用deposit方法向Bank合约发送10wei

（3）之后Attack合约调用withdraw方法，从而调用了Bank的withdrawBalance方法。

（4）Bank的withdrawBalance方法发送给了Attack合约10wei

（5）Attack收到10wei之后，又会触发调用fallback函数

（6）这时，fallback函数又调用了两次Bank合约的withdrawBalance，从而转走了20wei

（7）之后Bank合约才修改Attack合约的balance，将其置为0

通过上面的步骤，攻击者实际上从Bank合约转走了30wei，Bank则损失了20wei，如果攻击者多嵌套调用几次withdrawBalance，完全可以将Bank合约中的以太币全部转走。

复现过程

给Bank合约100wei，给Attack合约10wei。

（1）部署Bank，分配100wei

![img](https://z374q654zv.feishu.cn/space/api/box/stream/download/asynccode/?code=ZTlmZTFiMGVmZTUzOGRjM2ZlMTdlYmU0NTliZDY2MWFfdFdYZ2xwakFuY09CdnRIN1hpRXEwVFpjMUEwODRzQjdfVG9rZW46Ym94Y240M0ZjUXBXa0Z6UDdUd0F1eFVHOVhkXzE2NzU0MTU0MDA6MTY3NTQxOTAwMF9WNA)

（2）部署Attack

![img](https://z374q654zv.feishu.cn/space/api/box/stream/download/asynccode/?code=MDg4ZTc4MGY2Y2ZmZjhlYjJjZjRlNDc1YTJlZDcwMDRfdkRBSGY1S0kwNjBIOWpLTFFHYkpaZmlTbndmTG5ZSkVfVG9rZW46Ym94Y25OTTA1cjA0Um9mc1p4SUZqajg1d0FkXzE2NzU0MTU0MDA6MTY3NTQxOTAwMF9WNA)

分配给Attack 10wei。

（3）调用Attack合约的deposit方法

（4）调用Attack合约的withdraw方法

（5）查看Attack合约的余额，变成了30wei，即窃取了20wei

DAO攻击事件代码分析

在DAO源码中，有withdrawRewardFor函数：

这里调用了payOut函数进行付款。

而payOut中直接使用的是message call的方式发送以太币，从而导致了嵌套漏洞。

总结

在写智能合约进行以太币发送的时候，要使用send或者transfer，不要使用message call的方式。