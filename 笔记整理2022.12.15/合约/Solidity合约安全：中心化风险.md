# Solidity 合约安全：中心化风险

## 中心化风险

1. 我们经常以 Web3 的去中心化为骄傲，认为在 Web3.0 世界里，所有权和控制权都是去中心化。但实际上，中心化是 Web3 项目最常见的风险之一。知名区块链审计公司 Certik 在 2021 年 DeFi 安全报告中指出：
   中心化风险是 DeFi 中最常见的漏洞，2021 年中有 44 次 DeFi 黑客攻击与它相关，造成用户资金损失超过 13 亿美元。这强调了权力下放的重要性，许多项目仍需努力实现这一目标。
   中心化风险指智能合约的所有权是中心化的，例如合约的 owner 由一个地址控制，它可以随意修改合约参数，甚至提取用户资金。中心化的项目存在单点风险，可以被恶意开发者（内鬼）或黑客利用，只需要获取具有控制权限地址的私钥之后，就可以通过 rug-pull，无限铸币，或其他类型方法盗取资金。
   链游项目 Vulcan Forged 在 2021 年 12 月因私钥泄露被盗 1.4 亿美元，DeFi 项目 EasyFi 在 2021 年 4 月因私钥泄露被盗 5900 万美元，DeFi 项目 bZx 在钓鱼攻击中私钥泄露被盗 5500 万美元。

## 伪去中心化风险

1. 伪去中心化的项目通常对外鼓吹自己是去中心化的，但实际上和中心化项目一样存在单点风险。比如使用多签钱包来管理智能合约，几个多签人是一致行动人，背后由一个人控制。这类项目由于包装的很去中心化，容易得到投资者信任，所以当黑客事件发生时，被盗金额也往往更大。
2. 近两年爆火的链游项目 Axie 的 Ronin 链跨链桥项目在 2022 年 3 月被盗 6.24 亿美元，是历史上被盗金额最大的事件。Ronin 跨链桥由 9 个验证者维护，必须有 5 个人达成共识才能批准存款和提款交易。这看起来像多签一样，非常去中心化。但实际上其中 4 个验证者由 Axie 的开发公司 Sky Mavis 控制，而另 1 个由 Axie DAO 控制的验证者也批准了 Sky Mavis 验证节点代表他们签署交易。因此，在攻击者获取了 Sky Mavis 的私钥后（具体方法未披露），就可以控制 5 个验证节点，授权盗走了 173,600 ETH 和 2550 万 USDC。
   Harmony 公链的跨链桥在 2022 年 6 月被盗 1 亿美元。Harmony 桥由 5 个多签人控制，很离谱的是只需其中 2 个人签名就可以批准一笔交易。在黑客设法盗取两个多签人的私钥后，将用户质押的资产盗空。

## 漏洞合约例子

1. 有中心化风险的合约多种多样，这里只举一个最常见的例子：owner 地址可以任意铸造代币的 ERC20 合约。当项目内鬼或黑客取得 owner 的私钥后，可以无限铸币，造成投资人大量损失。
   // SPDX-License-Identifier: MIT
   pragma solidity ^0.8.4;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract Centralization is ERC20, Ownable {
constructor() ERC20("Centralization", "Cent") {
address exposedAccount = 0xe16C1623c1AA7D919cd2241d8b36d9E79C1Be2A2;
transferOwnership(exposedAccount);
}

function mint(address to, uint256 amount) external onlyOwner{
\_mint(to, amount);
}
}

## 总结

1. 中心化/伪去中心化是区块链项目最大的风险，近两年造成用户资金损失超过 20 亿美元。中心化风险通过分析合约代码就可以发现，而伪去中心化风险藏的更深，需要对项目进行细致的尽职调查才能发现。
