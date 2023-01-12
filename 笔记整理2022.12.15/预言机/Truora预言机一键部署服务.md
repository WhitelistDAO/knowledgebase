#Truora 预言机一键部署服务

1. FISCO-BCOS 提供一个一键部署脚本(deploy-all.sh ),我们可以直接使用这个脚本简化我们的部署步骤，脚本里面包括了 FISCO-BCOS 联盟链，WeBASE-Front 智能合约可视化开发平台，Truora-Service 预言机后端服务，Truora-Web 预言机前端服务以及 MySQL 数据库。

## Truora 一键部署工具特征

1. 提供自动安装依赖服务功能，包括：OpenSSL, curl, wget, Docker, Docker Compose 等
2. 调用 FISCO-BCOS 一键部署脚本 build_chain.sh，部署 4 个区块链底层节点
3. 部署 WeBASE-Front 服务
4. 部署 Truora-Service, Truora-Web 服务
5. 部署 MySQL 服务
6. 支持国密选项

## 获取部署脚本

1. 从 GitHub 下载最新部署包 wget "https://github.com/WeBankBlockchain/TruoraService/releases/download/v1.1.0/docker-deploy.zip"
2. 解压部署包 unzip docker-deploy.zip

## 执行部署脚本

1. 自动安装依赖服务，默认从 CDN 拉取 Docker 镜像# Docker Hub 官方仓库拉取镜像时，不仅速度比较慢，同时成功率也相对较低# # -d : 自动安装系统依赖# -g : 使用国密# -k : 从 Docker Hub 官方仓库拉取 Docker 镜像 bash deploy_all.sh -d

## 预言机请求数据演示

1. 先拿到预言机合约地址
2. 部署获取天气数据合约
3. 部署完成后，先调用 setUrl 方法设置我们自己的数据源
4. 调用 request 方法获取数据并存在链上（调用用户为 dsl）
5. 调用 get 方法查看数据

## 总结

1. 预言机的作用就是可以使得区块链能实时调用链下数据，没有预言机的情况是人工去传数据，使用预言机后，直接调用预言机合约的接口就能拿到链下数据，并且预言机通过内部共识和数据筛选算法保证数据的可信性，值得一提的是，国内预言机现在使用的数据源都是权威机构或政府背书的，但是对于小型数据源或国外数据源就没有这个效果了，目前团队也正在攻坚预言机数据源可信板块。
