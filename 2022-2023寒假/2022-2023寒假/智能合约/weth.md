WETH (Wrapped ETH)是ETH的带包装版本。我们常见的WETH，WBTC，WBNB，都是带包装的原生代币。那么我们为什么要包装它们？
在2015年，ERC20标准出现，该代币标准旨在为以太坊上的代币制定一套标准化的规则，从而简化了新代币的发布，并使区块链上的所有代币相互可比。不幸的是，以太币本身并不符合ERC20标准。WETH的开发是为了提高区块链之间的互操作性 ，并使ETH可用于去中心化应用程序（dApps）。它就像是给原生代币穿了一件智能合约做的衣服：穿上衣服的时候，就变成了WETH，符合ERC20同质化代币标准，可以跨链，可以用于dApp；脱下衣服，它可1:1兑换ETH。
WETH合约
目前在用的主网WETH合约写于2015年，非常老，那时候solidity是0.4版本。我们用0.8版本重新写一个WETH。
WETH符合ERC20标准，它比普通的ERC20多了两个功能：
1. 存款：包装，用户将ETH存入WETH合约，并获得等量的WETH。
2. 取款：拆包装，用户销毁WETH，并获得等量的ETH。
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract WETH is ERC20{
    // 事件：存款和取款
    event  Deposit(address indexed dst, uint wad);
    event  Withdrawal(address indexed src, uint wad);

    // 构造函数，初始化ERC20的名字和代号
    constructor() ERC20("WETH", "WETH"){
    }

    // 回调函数，当用户往WETH合约转ETH时，会触发deposit()函数
    fallback() external payable {
        deposit();
    }
    // 回调函数，当用户往WETH合约转ETH时，会触发deposit()函数
    receive() external payable {
        deposit();
    }

    // 存款函数，当用户存入ETH时，给他铸造等量的WETH
    function deposit() public payable {
        _mint(msg.sender, msg.value);
        emit Deposit(msg.sender, msg.value);
    }

    // 提款函数，用户销毁WETH，取回等量的ETH
    function withdraw(uint amount) public {
        require(balanceOf(msg.sender) >= amount);
        _burn(msg.sender, amount);
        payable(msg.sender).transfer(amount);
        emit Withdrawal(msg.sender, amount);
    }
}
继承
WETH符合ERC20代币标准，因此WETH合约继承了ERC20合约。
事件
WETH合约共有2个事件：
1. Deposit：存款事件，在存款的时候释放。
2. Withdraw：取款事件，在取款的时候释放。
函数
除了ERC20标准的函数外，WETH合约有5个函数：
- 构造函数：初始化WETH的名字和代号。
- 回调函数：fallback()和receive()，当用户往WETH合约转ETH的时候，会自动触发deposit()存款函数，获得等量的WETH。
- deposit()：存款函数，当用户存入ETH时，给他铸造等量的WETH。
- withdraw()：取款函数，让用户销毁WETH，并归还等量的ETH。
