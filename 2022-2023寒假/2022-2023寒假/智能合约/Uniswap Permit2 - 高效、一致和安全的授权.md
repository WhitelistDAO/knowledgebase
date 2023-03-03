## Uniswap Permit2 - 高效、一致和安全的授权

前几天，Uniswap Labs 发布了两个新的智能合约 Permit2 和 Universal Router :

1. Permit2 允许代币授权在不同的应用程序中共享和管理，创造一个更统一、更具成本效益、更安全的用户体验。
2. Universal Router 将 ERC20 和 NFT 兑换统一到一个单一的兑换路由器。与 Permit2 整合后，用户可以在一次兑换交易中兑换多个代币和 NFT，同时节省 Gas 费。

Uniswap 最初构思 Permit2 和 Universal Router 是为了改进 Uniswap 自己的产品，优化 Gas 成本，简化用户交易流程，并加强安全性。在构思的过程中，Unswap 觉得其他应用可以从整合这些合约中大大受益。Uniswap 本身致力于建设公共基础设施，因此设计了这些合约，提供整个开发者生态系统使用，包括广泛的文档、SDK。

Permit2 是一个代币授权合约，可以在不同的智能合约中安全地共享和管理代币授权。随着越来越多的项目与 Permit2 集成，可以在所有应用程序中对代币授权进行标准化。反过来，Permit2 将通过降低交易成本来改善用户体验，同时提高智能合约的安全性。

## 典型授权模式：

以下是EIP-20 中定义的典型代币授权（Approve）方法图示：

![img](https://z374q654zv.feishu.cn/space/api/box/stream/download/asynccode/?code=NjVlZTIzMTI0YmQ2YjUzNjg5NWI3OTUyOGY2MDhjMTFfbVZ1Nkw0UXhnUmZwbnVjcXJjcnBnU2kzMFZVQ1ZueGZfVG9rZW46Ym94Y25WT0c2Sml2U0xDdTYydEtXSjVDamdjXzE2NzU0MTU1MTg6MTY3NTQxOTExOF9WNA)

1. Alice 在一个 ERC20 上调用`approve()`，向一个合约授予支出授权。
2. Alice 在合约上调用一个交互函数，该函数又在 ERC20 代币合约上调用`transferFrom()`，转账她的代币。

显然，这种模式是可行的（它无处不在），并且最终可以相当灵活，因为协议通常会最终不间断地长期访问用户的代币。但它有两个众所周知的现实世界的问题。

- **糟糕的用户体验**， 用户必须对他们打算使用的每个代币授权给每个新的协议，这导致了混乱的用户体验，同时这几乎总是一个单独的交易，浪费了 Gas 和时间。
- **糟糕的安全性**，应用程序通常要求用户授权最大限额，以避免重复上述用户体验问题。这意味着，如果协议被利用，每个用户授权协议使用的代币都有可能被直接从他们的钱包里拿走（因为应用程序可以无限期地访问钱包的整个代币余额）。

## 授权签名（EIP-2612）模型：

EIP-2612 对代币的授权进行了迭代。用户可以通过在他们的交易中附加一个授权签名（Permit）信息来与应用合约交互，而不需要事先授权。

让我们看看 ERC20 的 EIP-2612 扩展所启用的方法，它通常是这样的：

![img](https://z374q654zv.feishu.cn/space/api/box/stream/download/asynccode/?code=OTRkOTU5ZmM0MmQ4MDlkMzhiOWU3YWRiYTAzYTdlYTRfa2ZhRHdTNkR3ZENCbkptcFMzc3g1RjVGVXZta01tWVBfVG9rZW46Ym94Y25xR1pvd1JsQWk0YWw5UHZLdnowMUZVXzE2NzU0MTU1MTg6MTY3NTQxOTExOF9WNA)

1. Alice 签署一个链外的 "permit(签名授权)" 信息，表示她希望授予一个合约一个（EIP-2612）代币的使用权。
2. Alice 提交签署的消息，作为她与所述合约交互的一部分。
3. 合约调用代币上的 "permit()" 方法，它会使用签名授权信息和签名，同时授予合约一个授权。
4. 合约现在有了授权，所以它可以在代币上调用`transferFrom()`，转账由 Alice 持有的代币。

这解决了典型 ERC20 授权方法的两个问题：

- 用户永远不需要提交一个单独的`approve()`交易。
- 不再有悬空授权的必要之恶，因为许可消息授予的是即时授权，通常会立即花费。因此也可以选择一个更合理的授权额度，更重要的是，在签名授权消息可以被使用代币的时间上有一个到期时间。

虽然 EIP-2612 使代币授权更加安全，但在 EIP-2612 之前推出的代币并不支持签名授权功能，而且并非所有较新的代币都采用该功能，这就是悲催的现实。因此大多数时候，这种方法不可行。

## Permit2 模式：

最后，让我们深入探讨 Permit2 的方法，Permit2 结合了这两种模式，将 EIP-2612 的用户体验和安全优势扩展到也涵盖了普通的 ERC20 代币!

为了说明 Permit2 的革命性，在一个常见的场景中，协议需要转账 Alice 持有的代币。

![img](https://z374q654zv.feishu.cn/space/api/box/stream/download/asynccode/?code=OWZjNTIzN2Y3NzBmMzY3ZWU0MTlmODA4ZDI2ODczOTdfdFBPQ0hTUExrZFJOTHhCZW1WZW1JYVBKaDhZandqa1dfVG9rZW46Ym94Y24wbjhUUktYWno5dXNzWXhXUXIxc3RnXzE2NzU0MTU1MTg6MTY3NTQxOTExOF9WNA)

1. Alice 在一个 ERC20 上调用`approve()`，典型的方式为的 Permit2 合约授予一个无限的授权。
2. Alice 签署一个链下 `permit2` 消息，该消息表明协议合约被允许代表她转账代币。
3. Alice 在协议合约上调用一个交互函数，将签署的 permit2 消息作为参数传入。
4. 协议合约在 Permit2 合约上调用 `permitTransferFrom()`，而 Permit2 合约又使用其授权（在 1 中授予）在 ERC20 合约上调用 "transferFrom()"，转账 Alice 持有的代币。

要求用户首先授予一个明确的授权交易，这似乎是一种倒退。但是，用户不是直接授予协议，而是将其授予规范的 Permit2 合约。这意味着，如果用户之前已经这样做了，比如说与另一个集成了 Permit2 的协议进行交互，那么其他每一个协议都可以跳过这个步骤。

这太棒了。

协议不会直接调用 ERC20 代币上的`transferFrom()`来执行转账，而是调用规范的 Permit2 合约上的`permitTransferFrom()`。Permit2 位于协议和 ERC20 代币之间，跟踪和验证 permit2 消息，然后最终使用其授权直接在 ERC20 上执行`transferFrom()`调用。这种间接性使得 Permit2 可以将类似于 EIP-2612 的好处扩展到每一个现有的 ERC20 代币上。

同时，像 EIP-2612 签名授权信息一样，Permit2 信息也会过期，以限制漏洞的攻击窗口。