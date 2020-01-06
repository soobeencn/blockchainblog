# 联盟链应用

## 上帝视角

### 理论基础

- 基本原理和架构
- 发展阶段
- 钱包安装和使用

	- 挖矿
	- 矿池

- 比特币/以太坊分叉原理和案例
- 拜占庭将军问题
- 价值转移
- 公有链 私有链 联盟链
- UTXO
- Hash算法 Merkle Tree
- 共识协议

	- pow
	- pos
	- dpos

- 时间戳
- 智能合约;DAO组织
- 软分叉/硬分叉
- 以太坊中心化/去中心化

### 数学基础

- 二进制
- 真随机数
- hash运算和数字加密
- 对称加密和非对称加密/数字签名
- 高阶模操作
- 指数和对数
- 非对称加密算法原理解析
- ASCII编码
- Base64和Base54
- Checksum
- 比特币钱包和Vanity地址
- 点对点网络的挑战
- double speding 问题
- ledger账本系统和UTXO方案
- 挖矿和区块链形成
- TestNet
- BIP比特币优化协议和行业标准

### 以太坊和智能区块链

- 白皮书了解
- 以太坊客户端install＆run
- 以太坊网络
- 智能合约编程入门
- solidity

	- 变量
	- 方法
	- 继承
	- 事件

- oracle(先知)
- solidity编程实战
- 以太坊前端框架web3.0
- 以太坊+ipfs实现去中心化webapp

### IBM超级账本

- HyperLedger介绍/区块链3.0
- hyperledger febric架构
- hyperledger febric集群平台搭建
- hyperledger febric智能合约链码开发
- hyperledger febric部署
- hyperledger fabric企业积分管理项目实战

## BitCoin

### 区块链应用场景

- 资产

	- 数字资产发行
	- 跨境支付
	- 交易
	- 结算

- 记账

	- 股权交易
	- 供应链金融
	- 商业积分

- 不可篡改

	- 溯源
	- 众筹
	- 医疗证明
	- 存在性证明

- 点对点

	- 共享经济
	- 物联网

- 隐私

	- 匿名交易

### 比特币是什么

- 数字货币

	- 一串数字

- 信任从何而来

	- 财产只受自己控制

- 无通胀
- 没有假钞
- 流通性好

	- 点对点直接到账

### 比特币原理

## Ethereum

### 特点

- 可编程的操作系统
- 智能合约
- 15s出块
- 无总量限制

### 核心概念

- account

	- EOA
	- contract account

### 区块链浏览器

## Hyperledger

### Burrow

### Sawtooth

### Fabric

- 核心技术

	- 整体架构

		- 网络拓扑

			- 客户端

				- 连接Orderer进行通道创建
				- 连接Peer节点进行交易创建及合约执行

			- Peer

				- Anchor

					- 锚节点

						- 组织内部唯一与Orderer进行通信的节点

							- 由配置指定

								- 可选举产生

				- Endorser

					- 背书节点

						- 为交易作担保

							- 运行智能合约

				- Committer

					- 记账节点

						- 用于验证从Orderer得到的区块和交易信息

			- Orderer

				- 0.6版本实现了PBFT

					- 1.0版本上线前剔除了
					- PBFT需要五步投票

				- 排序
				- 打包区块

					- 分发给组织节点

				- 两种节点

					- solo

						- 开发测试试用

							- 单点故障

					- kafka

						- 一个orderer同为生产者和消费者

			- CA(可选)

				- 建立区块链身份
				- 例如中国的CFCA

		- 共识机制

			- 所谓共识

				- 交易背书(模拟@Endorser)
				- 交易排序(排序@Orderer)
				- 交易验证(验证@Committer)

			- Orderer

				- 交易排序

					- solo:单节点排序,所见即所得
					- kafka:外置消息队列保证一致性

				- 区块分发

					- 中间状态区块(非落盘区块)
					- 有效交易&无效交易

				- 多通道数据隔离

					- Fabric独占

			- CAP原理

				- 达成消息一致
				- 强
				- 弱

- 智能合约

	- 引入

		- 以太坊
		- 合约协议的数字化代码表达

			- 欠条
			- 房屋合同

		- 分布式有限状态机
		- 执行环境安全隔离、不受第三方干扰

			- EVM
			- Docker

	- 链码

		- Fabric应用层基石(中间件)
		- 执行环境

			- 独立的Dokcer容器

				- 通过gRPC接口连接背书节点
				- 生命周期管理

					- 打包
					- 安装
					- 实例化

						- 实现一次

					- 升级

						- Fabric的优势

					- 交互

						- 查询
						- 写入

		- 系统链码

			- LSCC

				- Lifecycle System Chaincode

					- 管理安装实例化
					- 系统升级
					- docker打包链码不受其控制

			- CSCC

				- Configuration System Chaincode

					- 管理某一条链的配置

						- 允许新的配置加入某一条链

			- QSCC

				- Query System Chaincode

					- 查询区块索引的外部服务

			- ESCC

				- Endorsement System Chaincode

					- 交易背书链码

						- 将交易模拟执行后的结果进行封装+签名
						- 然后组装成客户端认识的交易背书结果

			- VSCC

				- Validation System Chaincode

					- 交易验证

						- 有效验证

		- 链码编程接口

			- init()

				- 初始化
				- 只执行一次

			- invoke()

				- 应用程序与链码交互的入口

		- 链码执行禁忌

			- 注意要素

				- 分布式系统
				- 多个节点隔离执行
				- 一条交易执行多次

					- 次数取决于背书策略的选择

			- 随机函数
			- 系统时间
			- 不稳定的外部依赖

- 网络搭建

	- 环境准备

		- docker

			- fabric-peer
			- fabric-orderer
			- fabric-baseos
			- fabric-tools
			- fabric-ca

		- fabric源码库

			- 版本切换(必要条件)

		- 生成两种工具

			- cryptogen

				- 生成相关证书

			- configtxgen

				- 生成创始区块
				- 生成通道配置

		- fabric-samples

			- 构建配置文件

				- ./byfn -m generate

			- 启动网络

				- ./byfn -m up

			- 关闭网络

				- ./byfn -m down

	- 配置文件准备

		- crypto-config.yaml

			- 配置组织节点的个数
			- 配置每个组织节点内部用户的个数

				- 生成相应的证书

		- configtx.yaml

			- 配置联盟中的组织信息

				- 组织名字
				- 证书

	- 创世配置构造

		- MSP证书
		- Orderer创世区块

			- 排序节点

		- Channel创世交易(区块)
		- 组织主节点配置文件

			- 非必需
			- 不设置,gossip协议会自动选举一个主节点出来

	- 网络启动

		- 节点进程启动

			- 创建Channel
			- 加入Channel
			- 安装Chaincode
			- 实例化Chaincode
			- Chaincode交互测试

- 源码解读

	- 排序接口
	- 基于Solo的排序服务
	- 基于Kafka的排序服务
	- 自由实现排序服务

- 拓展

	- 并不能完全满足企业需求
	- bccsp密码学

		- 需要手动添加国密gm

	- 区块链即服务(Baas)
	- 性能优化

		- fabric1.0(难题)

			- 100~300TPS

- 面试重点

	- 基础知识

		- Peer、Endorser、Committer关系

			- Peer

				- 通道
				- 同步账本

			- Endorser

				- 运行ChainCode 

			- Committer

				- 记账节点 

		- 节点、通道、链码关系

			- 火车站
			- 轨道
			- 火车

		- kafka如何保证多个orderer出块一致

			- 从官方文档入手,官方是如何设计   

		- committer如何验证区块提交的交易的有效性

			- 考察交易读写集的读集与写集是否一致

	- 交易

		- 交易模拟

			- 客户端发起
			- 背书节点执行链码
			- 生成交易读写集

				- 客户端收到response会发送到order节点进行排序

		- 交易排序

			- order节点排序完成会将生成好的临时区块广播到其他组织的主节点

		- 交易提交

			- 验证签名
			- 验证背书策略
			- 验证交易读写集

	- 账本存储

		- 文件系统存储区块

			- 更新索引

				- levelDB

	- 设计思考

		- 如何看待Fabric先执行后排序的设计思路

			- 交易模拟
			- 交易排序
			- 交易提交

		- 如何评价链码

			- 过于抽象

				- KV

			- 为了让链码能够独立运行,使用docker对链码构建镜像

- 去中心化资产交易平台

	- 团队需求

		- 安全组
		- 架构
		- 产品
		- 设计
		- 前端
		- 后台
		- 运维

	- 实现效果

		- 用户注册
		- 用户登记
		- 资产转让
		- 资产查询
		- 用户查询
		- 资产转让历史查询
		- 用户删除

	- 开发流程

		- 需求整理

			- 数据上链
			- 交易方法

		- 链码编写
		- 链码交互

- 总结

	- 区块链的未来

		- 法律
		- 社会
		- 经济
		- 人性
		- 技术的矛盾体

	- 区块链的定位

		- 云计算

			- 为我们提供IT基础设施

		- 区块链

			- 可信数据的载体,可以防止大数据的误入歧途

		- 大数据

			- 拥有了可信数据,才可以拿到有价值的数据,才可以进行个性化定制

		- 人工智能

			- 有效决策

	- 区块链应用阶段

		- 数据认证
		- 数据转移
		- 数据共享

- 李工问题

	- 性能测试

		- PTE
		- wrt
		- ab
		- blockbench
		- caliper

			- 成功

	- Fab3

		- 支持以太坊接口查询

			- signlogintransaction
			- 签名

		- 启动流程

			- 启动Fab3

				- 暴露5000端口

			- 使用web3服务,连接Fab3暴露的端口
			- 执行web3命令

	- 跨链

		- chaincodeA

			- ==

				- chaincodeB

	- 主链手续费问题

		- 无手续费激励
		- 10个交易确认一个区块

			- 共识算法的问题

		- 2s出块

			- 共识算法的问题

	- 区块浏览器

		- 可根据源码执行

			- 已完成

- 多机多节点部署

	- orderer

		- orderer.example.com

	- org1

		- peer0

			- peer0.org1.example.com

		- peer1

			- peer1.org1.example.com

	- org2

		- peer0

			- peer0.org2.example.com

		- peer1

			- peer1.org2.example.com

- Dapp

	- 智能合约
	- 前端

### INDY

