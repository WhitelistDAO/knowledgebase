# Go调用智能合约

## **一、安装geth**

![IMG_256](media/image1.png){width="5.885416666666667in"
height="2.2708333333333335in"}

## **二、Geth私有链搭建**

### **1.创建genensis.json文件，放在geth所安装的目录**

{

\"config\": {

\"chainId\": 15,

\"homesteadBlock\": 0,

\"eip150Block\": 0,

\"eip150Hash\":
\"0x0000000000000000000000000000000000000000000000000000000000000000\",

\"eip155Block\": 0,

\"eip158Block\": 0,

\"byzantiumBlock\": 0,

\"constantinopleBlock\": 0,

\"petersburgBlock\": 0,

\"istanbulBlock\": 0,

\"ethash\": {}

},

\"nonce\": \"0x0\",

\"timestamp\": \"0x5ddf8f3e\",

\"extraData\":
\"0x0000000000000000000000000000000000000000000000000000000000000000\",

\"gasLimit\": \"0x47b760\",

\"difficulty\": \"0x00002\",

\"mixHash\":
\"0x0000000000000000000000000000000000000000000000000000000000000000\",

\"coinbase\": \"0x0000000000000000000000000000000000000000\",

\"alloc\": { },

\"number\": \"0x0\",

\"gasUsed\": \"0x0\",

\"parentHash\":
\"0x0000000000000000000000000000000000000000000000000000000000000000\"

}

![IMG_257](media/image2.png){width="6.541666666666667in"
height="2.1458333333333335in"}

### **2.初始化区块链**

geth init \--datadir node1 genesis.json
*//\"\--datadir\"是指定数据存放的目录，例如node1*

![IMG_258](media/image3.png){width="7.208333333333333in"
height="2.03125in"}

Successfully ancient database 代表geth初始化成功

### **3.启动私有节点**

geth \--datadir \"xxxx\" \--networkid ???? \--port ????? \--http
\--http.api \"eth,net,web3,miner,admin\" \--http.addr \"localhost\"
\--http.port ???? console \--nodiscover 2\>node1.log

geth \--datadir \"node1\" \--networkid 6666 \--port 10010 \--http
\--http.api \"eth,net,web3,miner,admin,personal\" \--http.addr
\"localhost\" \--http.port 8545 \--http.corsdomain
https://remix.ethereum.org \--allow-insecure-unlock console 2\>node1.log

（1）\--networkid后面跟数字，表示指定这个私有链的网络id。以太坊公网的网络id是1，为了不与公有链网络冲突，运行私有链节点的时候要指定自己的网络id并且不能为1；

（2）2\>node1.log就是将日志信息输出到当前目录下的output.log的文件里；

![IMG_259](media/image4.png){width="6.934722222222222in"
height="1.5354166666666667in"}

（3）运行节点时，\--http.corsdomain
[https://remix.ethereum.org让节点可以连接remix，进行合约调用，但超过字节限制的合约是无法挖矿成功，以至于无法部署。]{.underline}

（4）remix连接端口

![IMG_260](media/image5.png){width="6.945833333333334in"
height="3.779861111111111in"}

这里我已经创建了两个用户，并一个用户进行了挖矿

![IMG_261](media/image6.png){width="7.029861111111111in"
height="0.6298611111111111in"}

![IMG_262](media/image7.png){width="3.1458333333333335in"
height="1.0416666666666667in"}

部署合约时账户是锁住的，需要解锁

![IMG_263](media/image8.png){width="6.979166666666667in"
height="0.5625in"}

正在挖矿

![IMG_264](media/image9.png){width="3.40625in"
height="0.4166666666666667in"}

学习误区：一直未挖矿成功

### **4.账户交易**

#### 创建用户

Personal.newAccount("xxx")

![IMG_265](media/image10.png){width="7.208333333333333in"
height="1.7916666666666667in"}

查询当前节点下所有的账户

eth.accounts

![IMG_266](media/image11.png){width="7.21875in" height="1.15625in"}

#### 挖矿操作

开始挖矿：miner.start()或者miner.start（X）

确认挖矿状态：eth.mining

停止挖矿：miner.stop()

设置挖矿账户miner.setEtherbase(accounts\[?\])

![IMG_267](media/image12.png){width="6.635416666666667in"
height="3.34375in"}

#### 查看区块和余额

eth.blockNumber

eth.getBalance(eth.accounts\[0\])*//这里的accounts\[0\]就是我们创建的第一个账户*

![IMG_268](media/image13.png){width="5.645833333333333in"
height="1.1458333333333333in"}

#### 转账交易

解锁账户:personal.unlockAccount(eth.accounts\[?\]);

转账:eth.sendTransaction({from: eth.accounts\[?\], to:
eth.accounts\[?\],value: web3.toWei(?,\"ether\")})

查看交易:txpool.status

查看交易详情:txpool.inspect.pending

挖矿交易:miner.start(1);admin.sleepBlocks(1);miner.stop();

![IMG_269](media/image14.png){width="7.208333333333333in"
height="3.9479166666666665in"}

![IMG_270](media/image15.png){width="7.208333333333333in"
height="2.3020833333333335in"}

## **三、使用abigen将智能合约转为go**

### 1.使用solcjs 生成abi 和 bin

![IMG_271](media/image16.png){width="7.197916666666667in"
height="0.71875in"}

### 2.使用abigen将上诉两个文件转go

abigen \--bin demo_sol_MyContract.bin \--bin demo_sol_MyContract.abi
\--pkg main \--type Demo \--out demo.go

![IMG_272](media/image17.png){width="7.208333333333333in"
height="0.4791666666666667in"}

### 3.使用创建账户

![IMG_273](media/image18.png){width="7.197916666666667in"
height="0.2916666666666667in"}

### 4.部署调用智能合约

package main

import (

\"context\"

\"fmt\"

\"github.com/ethereum/go-ethereum/accounts/abi/bind\"
\"github.com/ethereum/go-ethereum/ethclient\"

\"log\"

\"math/big\"

\"strings\"

\"time\"

)

const *keyinfo* =
\`{\"address\":\"46e3c75ae47f31abd24b2aef3c3fcf54d9636698\",\"crypto\":{\"cipher\":\"aes-128-ctr\",\"ciphertext\":\"b5665917f5df5dfd077d8d90f17b0abde36d8bb77d67302ddf76c7bf494d8c4c\",\"cipherparams\":{\"iv\":\"8b6b3aa5ceec9559575044dbb853ff6e\"},\"kdf\":\"scrypt\",\"kdfparams\":{\"dklen\":32,\"n\":4096,\"p\":6,\"r\":8,\"salt\":\"2cfde8437bcf959a31d7d0b0b07b8f922815c52fa5138ba6585d88a1b6eb529a\"},\"mac\":\"69f1140b51f2caec6fbb883663d1c67fecb3f55cc33464eb93a15f8945790f9c\"},\"id\":\"eda461d9-ddcb-4694-9345-d6dddc569f3e\",\"version\":3}\`

func main() {

//1.创建客户端

cli,err := ethclient.Dial(\"http://127.0.0.1:8545\")

if err != nil {

log.Panic(\"failed to Dial\",err)

}

//2.身份准备

keyin := strings.NewReader(*keyinfo*)

ChainID,err := cli.ChainID(context.Background()) auth,err1
:=bind.NewTransactorWithChainID(keyin,\"123\",ChainID)

if err1 != nil {

log.Panic(\"failed to NewTransctor\",err1)

}

//3.部署第二个合约

addr,tx,instance,err2:= DeployDemo(auth,cli)

if err2 != nil {

log.Panic(\"failed to Deploy Coupon2\",err2)

}

fmt.Println(\"addr:\",addr.Hex())

fmt.Println(\"tx:\",tx.Hash())

msg , err3 := instance.GetBlockNumber(&bind.CallOpts{Pending: true})

if err3 != nil {

log.Panic(\"failed\",err3)

}

fmt.Println(msg)

msg1 , err4 := instance.GetBlockHash(&bind.CallOpts{Pending:
true},big.NewInt(1072))

if err4 != nil {

log.Panic(\"getBlockhash failed\",err4)

}

fmt.Println(msg1)

fmt.Printf(\"TX start @:%s\", time.Now())

}

![IMG_274](media/image19.png){width="7.208333333333333in"
height="1.25in"}
