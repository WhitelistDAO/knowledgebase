## Ethers.js笔记整理
### Signer API
Signer是以太坊账户的抽象，可用于对消息和交易进行签名，并将已签名的交易发送到以太坊网络以执行状态更改操作。可用的操作在很大程度上取决于所使用的子类。

为了实现一个 签名器Signer , 需要继承抽象类 ethers.types.Signer 并实现下面的属性：
- object . provider
常见的signer实现有Wallet和JsonRpcSigner
object . getAddress ( )  =>  Promise(Address)
返回可获取账号地址的 promise 。
object . signMessage ( message )  =>  Promise(hex)
返回包含信息message    Flat-Format Signature 的 Promise 。
如果参数 message 是字符串, 它被转换为UTF-8字节，否则使用数据用 Arrayish 表示的二进制。
object . sendTransaction ( transaction )  =>  Promise(TransactionResponse)
发送 交易transaction到网络，返回一个 可以获取 Transaction Response 的 Promise 任何没有提供的属性将从网络获取填充。

### Wallet
wallet实现了signer,包含signer的所有属性
#### 创建Wallet
>从参数 privateKey 私钥创建一个钱包实例， 还可以提供一个可选的 提供者Provider 参数用于连接节点。
new Wallet ( privateKey [ , provider ] )
创建一个随机钱包实例。options 为：extraEntropy — 额外的熵加入随机
Wallet . createRandom ( [ options ] )  =>  Wallet
通过解密一个 JSON 钱包文件创建钱包实例
Wallet . fromEncryptedJson ( json, password [ , progressCallback ] )  =>  Wallet
通过助记词（及路径）创建钱包实例，
Wallet . fromMnemonic ( mnemonic [ , path = “m/44’/60’/0’/0/0” [ , wordlist ] ] )  =>  Wallet

prototype . connect ( provider )  =>  Wallet
从已有实例创建新的Wallet实例，并连接到新provider
#### wallet属性
>prototype . address 
Wallet 地址
prototype . privateKey
wallet私钥;
prototype . provider
返回连接的 Provider它允许钱包连接到以太坊网络以查询其状态并发送交易， 如果没有连接提供者，则返回null。
要更改 提供者Provider ，使用 connect方法，该方法将返回连接到新的 提供者Provider 的钱包 实例 。
prototype . mnemonic
返回钱包的助记词，如果没有助记词，则为null。
prototype . path
返回此钱包的助记词上的路径，如果没有助记词，则为null。

#### 签名方法
prototype . sign ( transaction )  =>  Promise(string)
对交易签名返回一个 Promise ，从Promise中可以获取一个签名后的交易hash 16进制字符串
通常应该使用 sendTransaction 而不是 sign, 因为它可以异步执行。
交易的属性（字段）都是可选的，包括：
>- to
>- gasLimit
>- gasPrice
>- nonce
>- data
>- value
>- chainId

prototype . signMessage ( message )  =>  Promise(string)
对 message 签名返回Promise,如果 message 是一个字符串, 它被转换为UTF-8字节，否则使用数据用 Arrayish 表示的二进制。
### Provider
Provider 是一个连接以太坊网络的抽象，用与查询以太坊网络状态或者发送更改状态的交易。
#### 连接以太坊网络
ethers . getDefaultProvider( [ network = “homestead” ] )  =>  Provider

new ethers . providers . JsonRpcProvider( [ urlOrInfo = “http://localhost:8545” ] [ , network ] )
通过节点 urlorInfo 的 JSON-RPC API URL 进行连接
参数： urlOrInfo 也可以作为一个对象，可以指定以下参数：
- url — JSON-RPC URL (必须)
- user — 用于基本身份验证的用户名 (可选)
- password — 用于基本身份验证的密码 (可选)
- allowInsecure — 允许通过不安全的HTTP网络进行基本身份验证 (默认值: false)
#### Provider的属性
prototype . blockNumber
>返回 提供者Provider 已经知晓的最新区块号（块高），如果没有同步到区块，则为 null。

- prototype . polling
- 可变的 mutable如果 提供者Provider 当前正在轮询，因为它活跃的观察事件。 轮询可以设置为临时启用/禁用或永久禁用以允许节点进程退出。
- prototype . pollingInterval
- 可变的 mutable提供者Provider 的轮询频率（以毫秒为单位）。 默认时间间隔为4秒。对于 PoA 网络本地节点时，更小的轮询间隔也许有意义。 不过但轮询Etherscan或INFURA时，设置得太低可能会导致服务阻止我们的IP地址或以其他方式限制API调用。
#### 获取网络
prototype . getNetwork ( )  =>  Promise(Network)
#### 获取账号信息
prototype . getBalance ( addressOrName [ , blockTag = “latest” ] )  =>  Promise(BigNumber)
prototype . getTransactionCount ( addressOrName [ , blockTag = “latest” ] )  =>  Promise(number)
返回参数 addressOrName 的所发送交易数量（Number对象）的 Promise，它用来作为发送交易的nonce。 可选参数 blockTag 指定一个区块
#### 获取以太坊状态
- prototype . getBlockNumber ( )  =>  Promise(number)
- 返回最新区块号（Number类型）的 Promise
- prototype . getGasPrice ( )  =>  Promise(BigNumber)
- 返回当前 gas 价格 (类型 BigNumber) 的Promise
- prototype . getBlock ( blockHashOrBlockNumber )  =>  Promise(Block)
- 返回给定参数对应的区块信息的 Promise
- prototype . getTransaction ( transactionHash )  =>  Promise(TransactionResponse)
- 根据交易hash获取交易信息的 Promise
- prototype . getTransactionReceipt ( transactionHash )  =>  Promise(TransactionReceipt)
- 根据交易hash获取交易收据的Promise

####  合约信息
- prototype . getCode ( addressOrName )  =>  Promise<hex>
- 返回Promise with the bytecode (as a hex string) at addressOrName.
- prototype . getStorageAt ( addressOrName , position [ , blockTag = “latest” ] )  =>  Promise<hex>
- 返回Promise  with the value (as a hex string) at addressOrName in position at blockTag. 
- prototype . getLogs ( filter )  =>  Promise< Log [ ] >
- 返回 Promise with an array (possibly empty) of the logs that match the filter. 

### 合约相关
#### 创建 ContractFactory
- new ethers . ContractFactory ( abi , bytecode [ , signer ] )
- 创建合约工厂，通过部署合约的 字节码 以及在 abi 中定义的构造函数来创建合约工厂。 签名器 singer 将用于发送任何部署交易
- ethers . ContractFactory . fromSolidity ( compilerOutput [ , signer ] )
从Solidity编译器的 compilerOutput 或从 Truffle JSON (如： output.contracts['SimpleStorage.sol'].SimpleStorage) 创建合约工厂 ContractFactory。
- prototype . connect ( signer )  =>  ContractFactory
创建连接到新 签名器signer 的ContractFactory 新实例 。

#### 属性
- prototype . bytecode
- 返回合约的可执行字节码。
- prototype . interface
- 返回合约应用程序二进制接口(ABI)。
- prototype . signer
- 返回signer用来发送交易到网络。 如果为空，则 deploy() 不可以调用。

#### 关联地址
- prototype . attach ( address )  =>  Contract
- 使用合约接口和签名器Singer 连接到现有合约实例。参数为合约 地址
#### 部署合约
- prototype . deploy ( … )  =>  Promise(Contract)
- 创建一个部署合约的交易，并使用合约Signer将其发送到网络，并返回合约对象的Promise。 交易记录通过 contract.deployTransaction 获取。 参数将传递给合约构造函数constructor。 请记住，合约可能不会立即挖出。 contract.deployed() 函数将返回一个Promise，将在部署合约后获取实例，或者在部署被拒绝时生成一个错误。
- prototype . getDeployTransaction ( … )  =>  UnsignedTransaction
- 返回部署合约（提供了构造参数）的交易。 通常可用于离线签名交易或分析工具。

#### 连接合约
- new ethers . Contract ( addressOrName , abi , providerOrSigner )
- 通过 abi 及 addressOrName 连接合约，连接作为 providerOrSigner。

#### 事件相关
##### 事件过滤器
prototype . filters . eventname
##### 配置事件