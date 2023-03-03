

## **Hardhat是智能合约开发笔记-hardhat入门什么？**

Hardhat是一个编译、部署、测试和调试以太坊应用的开发环境

#### **怎么安装？**

先安装nodejs环境;

然后打开命令行执行以下命令, 在项目目录pj_220509下安装hardhat环境：

![img](https://z374q654zv.feishu.cn/space/api/box/stream/download/asynccode/?code=NDgzODgxZTllMjY0OGMzMTMyN2M3YWZkMmJiNDBhOTZfWDZYUE1ZNnVnTkRpRTdTOWwwVmdIWXBtQTBwSWdWSTdfVG9rZW46Ym94Y25lWWw1OWRKMWxKSHNIQWNmYjB1WllmXzE2NzU0MTQ5NzU6MTY3NTQxODU3NV9WNA)

#### 怎么创建项目？

pj_220509目录下， 执行命令 npx hardhat

![img](https://z374q654zv.feishu.cn/space/api/box/stream/download/asynccode/?code=MWY5NDllNTNiYTkyMDA5YmM0MzcwZTVlODcyMGJiYTJfVlQ1UFh4TFAzSU9NU25YcDZNMUVWclRvbnlFd1JoUFlfVG9rZW46Ym94Y255M1lobm96emEzdzltdHFVeDZ1MEdoXzE2NzU0MTQ5NzU6MTY3NTQxODU3NV9WNA)

然后按提示安装相关的nodejs包，如下；完成安装；

装完了有啥用？怎么用？

装完后呀，可以在本地启动一个区块链本地环境节点，厉害吧；

装完后，可以编译你的智能合约，测试你的智能合约，部署你的智能合约到本地网、测试网、或主网。

还有提供一个控制台，即命令窗口，你可以用来和区块链、智能合约 “对话聊天”

项目的目录结构长啥样？

下图即是项目的目录结构，主要目录简单说明下：

contracts        合约源文件目录

scripts             部署脚本、交互脚本存放目录

test                测试程序目录

artifacts        编译后生成的目录

hardhat.config.js        配置文件

#### 怎么启动一个本地的区块链环境节点？

在pj_220509目录下，在命令行中执行

![img](https://z374q654zv.feishu.cn/space/api/box/stream/download/asynccode/?code=NGQ5OTczMWI3ZmRjNTYzNzg1MjhhNGY4M2RhNTBhZWVfR2xPTVE2dEtpM0xmMmRWTnNJQjFDWVNrT3FLVHNoNWFfVG9rZW46Ym94Y25zWWFXWEpRajQyWmJCelVtVmd2VlpiXzE2NzU0MTQ5NzU6MTY3NTQxODU3NV9WNA)

![img](https://z374q654zv.feishu.cn/space/api/box/stream/download/asynccode/?code=MTI5YWE2YzcyMDBkNzkxZWE3OWE0Y2FmYjdlYzZmYjJfM2JiWjdiYkt3dmlNRHpFcElORzhoTXh5U0lRVnhkaWNfVG9rZW46Ym94Y25WVnZ4dm0zakZ6bVZGbVBqSTBGeDRnXzE2NzU0MTQ5NzU6MTY3NTQxODU3NV9WNA)

怎么编译合约？

在pj_220509目录下，在命令行中执行： npx hardhat compile

将会编译项目中所有的合约；

怎么测试合约？

在pj_220509目录下，在命令行中执行： npx hardhat test

将会执行test目录所有的测试程序；

怎么部署合约到区块链网络上去？

在scripts目录下，写好部署脚本 deploy.js , 然后使用以下命令执行脚本进行部署，

--network localhost 参数用来指定连接到哪个网，这里是本地环境；（即上面用 npx hardhat node启动的本地环境）

![img](https://z374q654zv.feishu.cn/space/api/box/stream/download/asynccode/?code=MzVmMGU2YWIzZDkxOTdhZTYzZTM0ZjMxNzhmMjIwYjhfVVZNeWVQdVVNWDU5c1huZVNRQkhsR3I4RnZPRmlia3hfVG9rZW46Ym94Y251dzNyRG0wUUtUNjAycGFOamV0c1FlXzE2NzU0MTQ5NzU6MTY3NTQxODU3NV9WNA)