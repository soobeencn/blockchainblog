## 准入机制

### 背景
现状:
- 当下区块链环境是多节点部署到同一专有网络中, 这样没有发挥区块链共识机制的作用

为什么要接入准入机制:
- 多家机构共同参与共识,共同治理区块链网络,以达到安全、防篡改的目的.

### 目标
1. 节点加入准入机制;
2. 添加账户权限, 服务端与节点建立通信, 需要特定账户, 必须使用密钥.

### 方案

#### 网络配置
配置网络，以确保永远不会丢失1/3或更多验证器。如果超过1/3的验证器停止参与，则不再创建新块，网络将停滞。一旦节点重新启动，恢复可能需要很长时间。

最低节点数量: 4

最高节点数量: 32 

#### 整体架构
// 补充

_关于许可：许可型网络通过启用 **节点许可** 或 **帐户许可**，只允许指定的 **节点** 和 **帐户** 参与._

#### 节点许可                

- 确认节点数量,比如4个,这里的节点与机构(医院)对等
- 生成key和pubkey密钥文件

    ```
    ├── genesis.json
    └── keys
        ├── 0x4c1cac51f35d844fbcdcd39e58607bad9220da57
        │   ├── key
        │   └── key.pub
        ├── 0xac859b0fc658f326fa39e0fac1dd085c05e1ecd4
        │   ├── key
        │   └── key.pub
        ├── 0xd56107077aca91e0f8e792a42816aeb38d0d9f52
        │   ├── key
        │   └── key.pub
        └── 0xed176a37eef24de4affee204a1a89f0103d25dad
            ├── key
            └── key.pub
    ```
- 将4个节点分发至各个机构中, 比如: /Node-1/data/ 目录下              
- 启动任一节点, 启动后生成引导节点, 后续节点采取连接引导节点的方式启动
- 最后查看每个节点的日志, 判断是否参与共识.

备注: 只有通过同一genesisFile生成的密钥, 分发到机构中, 节点才可以参与到共识网络中.

#### 账户许可
- 现有: 
    - 当下账户转账(存证事务),不需要消耗gas, 也就是创建一个账户(account和privateKey)即可发起交易, 并会被节点识别并打包至区块中,并广播至全网.

- 修改: 
    - 节点启动时禁止使用命令: --min-gas-price=0
    - 创世区块中配置alloc账户及余额,使用genesis.json初始化后,删除alloc账户信息,确保安全,选择外部账户管理器进行管理授权
    - 交易设置: 
        - gasPrice=0L --> gasPrice=21000
    -  创建账户管理器, 管理账户
        1. 创建钱包 --> 根据助记词生成account与privateKey --> 记录
        2. 授权新的account
        3. 获取授权状态
        4. 通过新的account, 发起交易(事务:存证、取证、区块信息、交易信息)

-------------------------------------------

## 隐私网络设计

### 背景
企业以太坊中的隐私指的是在交易当事人之间保持交易秘密的能力。其他方无法访问交易内容、发送方或参与方列表。

### 目标
1. 通道隔离
2. 使用私有交易管理器来实现隐私网络

### 方案
#### 隐私网络部署图

![](http://mweb-storage.oss-cn-shenzhen.aliyuncs.com/2022/04/15/16432707557874.jpg?image/auto-orient,1/quality,Q_90)

具体流程:

- 部署4个区块链节点(准入机制)
- 部署4个Tessera节点,使用命令确认隐私组部署是否成功
- 创建隐私组
- 服务端指定端口与区块链节点建立通信
- 调用隐私组内成员提供的rpc服务,确认隐私交易只在组中生效

#### 隐私交易流程

1. 使用eea_sendRawTransaction提交私人交易。已签名的事务包括特定于私人事务的事务参数，包括：
    - privateFor或privacyGroupId，指定收件人列表。
    - privateFrom，它指定了发件人。
    - restriction，该指定交易仅限于交易参与者。
2. JSON-RPC端点将私有事务传递给私有事务处理程序。
3. 私人事务处理程序将私人事务发送到Tessera。
4. Tessera将私有交易直接（即点对点）分发给privateFor中指定的Tessera节点或属于privacyGroupId标识的隐私组。所有收件人Tessera节点都存储事务。Tessera将存储的事务与事务散列和隐私组ID相关联。
5. Tessera将事务散列返回给私人事务处理程序。
6. 私有事务处理程序为私有事务创建PMT。私有事务处理程序使用devP2P以与任何其他公共以太坊事务相同的方式传播PMT。
7. Besu将PMT挖掘成一个块，PMT分布到网络中的所有以太坊节点。
8. Mainnet事务处理器以与任何其他公共事务相同的方式处理PMT。在包含PMT属性中指定的隐私预编译合同的节点上，Mainnet事务处理器将PMT传递给隐私预编译合同。
9. 隐私预编译合同使用事务散列查询Tessera的私有事务和隐私组ID。

10. 隐私预编译合同将私人事务传递给私人事务处理器。隐私组ID指定要使用的私人世界状态。

11. 私有事务处理器执行事务。私人交易处理器可以读写给私人世界状态，并从公共世界状态读取

#### 性能测试报告

|Method|Name|# Requests|# Fails|Average (ms)|Min (ms)|Max (ms)|Average size (bytes)|RPS|Failures/s|
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
|POST|/pri-net/sendPriTransaction|10038|2|67|36|433|268|16.3|0.0|
||Aggregated|10038|2|67|36|433|268|16.3|0.0|
							
- 开源平台: locust
- 语言版本: python3.9
- 测试方式: 启动10、20、50个线程
- 测试结果: 测试1万条数据,大概写入性能在16tx/s左右,TPS为16




---------------------------
## 隐私网络部署

### 必要条件: 
#### 下载Hyperledger Besu
> https://github.com/hyperledger/besu/releases

寻找稳定版本, 下载已编译完成的安装文件.进入besu顶级目录.

#### 创建节点目录
```bash
IBFT-Network/
├── Node-1
│   ├── data
├── Node-2
│   ├── data
├── Node-3
│   ├── data
└── Node-4
    ├── data
```
#### 创建配置文件
```bash
{
 "genesis": {
   "config": {
      "chainId": 1337,
      "muirglacierblock": 0,
      "ibft2": {
        "blockperiodseconds": 2,
        "epochlength": 30000,
        "requesttimeoutseconds": 4
      }
    },
    "nonce": "0x0",
    "timestamp": "0x58ee40ba",
    "gasLimit": "0x47b760",
    "difficulty": "0x1",
    "mixHash": "0x63746963616c2062797a616e74696e65206661756c7420746f6c6572616e6365",
    "coinbase": "0x0000000000000000000000000000000000000000",
    "alloc": {
       "fe3b557e8fb62b89f4916b721be55ceb828dbd73": {
          "privateKey": "8f2a55949038a9610f50fb23b5883af3b4ecb3c3bb792cbcefbd1542c692be63",
          "comment": "private key and this comment are ignored.  In a real chain, the private key should NOT be stored",
          "balance": "0xad78ebc5ac6200000"
       },
       "627306090abaB3A6e1400e9345bC60c78a8BEf57": {
         "privateKey": "c87509a1c067bbde78beb793e6fa76530b6382a4c0241e5e4a9ec0a0f44dc0d3",
         "comment": "private key and this comment are ignored.  In a real chain, the private key should NOT be stored",
         "balance": "90000000000000000000000"
       },
       "f17f52151EbEF6C7334FAD080c5704D77216b732": {
         "privateKey": "ae6ae8e5ccbfb04590405997ee2d52d2b330726137b875053c36d94e974d162f",
         "comment": "private key and this comment are ignored.  In a real chain, the private key should NOT be stored",
         "balance": "90000000000000000000000"
       }
      }
 },
 "blockchain": {
   "nodes": {
     "generate": true,
       "count": 4
   }
 }
}
```
#### 创建节点密钥和创世文件
    生成节点密钥:
    > besu operator generate-blockchain-config --config-file=ibftConfigFile.json --to=networkFiles --private-key-file-name=key
#### 复制genesis.json 至 IBFT-Network 目录中
#### 复制节点私钥到节点目录中
```bash
IBFT-Network/
├── genesis.json
├── Node-1
│   ├── data
│   │    ├── key
│   │    ├── key.pub
├── Node-2
│   ├── data
│   │    ├── key
│   │    ├── key.pub
├── Node-3
│   ├── data
│   │    ├── key
│   │    ├── key.pub
├── Node-4
│   ├── data
│   │    ├── key
│   │    ├── key.pub
```

节点准备工作已完毕, 接下来是私钥管理器的安装与部署.

### 创建Tessera目录
在每个Node-*目录中，创建一个Tessera目录：
```bash
IBFT-Network/
├── Node-1
│   ├── data
│   ├── Tessera
├── Node-2
│   ├── data
│   ├── Tessera
├── Node-3
│   ├── data
│   ├── Tessera
└── Node-4
    ├── data
    ├── Tessera
```

### 生成Tessera密钥

- 必要条件: Tessera

  > https://github.com/ConsenSys/tessera/releases/tag/tessera-22.1.0

此示例创建一个解锁的私钥，这意味着不需要密码来解密私钥文件.
在每个Tessera目录中，为Tessera节点生成公钥/私钥对:
> java -jar ~/tessera-app-21.4.1-app.jar -keygen -filename nodeKey

在提示符处，按回车键以创建解锁密钥.
Tessera生成公钥/私钥对，并将密钥保存在nodeKey.pub和nodeKey.key文件中.

### 创建Tessera配置文件
在每个节点的Tessera目录中，创建一个名为tessera.conf的文件，配置如下：

备注: 
**在生产环境中，只有在其他运输安全机制（如WireGuard）到位时，才将tls指定为OFF。**

#### Tessera-01
```bash
{
   "mode": "orion",
   "useWhiteList": false,
   "jdbc": {
       "username": "sa",
       "password": "",
       "url": "jdbc:h2:./target/h2/tessera1",
       "autoCreateTables": true
   },
   "serverConfigs":[
       {
           "app":"ThirdParty",
           "serverAddress": "http://localhost:9101",
           "communicationType" : "REST"
       },
       {
           "app":"Q2T",
           "serverAddress": "http://localhost:9102",
           "communicationType" : "REST"
       },
       {
           "app":"P2P",
           "serverAddress":"http://localhost:9103",
           "sslConfig": {
               "tls": "OFF"
           },
           "communicationType" : "REST"
       }
   ],
   "peer": [
       {
           "url": "http://localhost:9203"
       },
       {
           "url": "http://localhost:9303"
       },
       {
           "url": "http://localhost:9403"
       }
   ],
    "keys": {
        "passwords": [],
        "keyData": [
            {
                "privateKeyPath": "nodeKey.key",
                "publicKeyPath": "nodeKey.pub"
            }
        ]
   },
   "alwaysSendTo": []
}
```
#### Tessera-02
```bash
{
   "mode": "orion",
   "useWhiteList": false,
   "jdbc": {
       "username": "sa",
       "password": "",
       "url": "jdbc:h2:./target/h2/tessera1",
       "autoCreateTables": true
   },
   "serverConfigs":[
       {
           "app":"ThirdParty",
           "serverAddress": "http://localhost:9201",
           "communicationType" : "REST"
       },
       {
           "app":"Q2T",
           "serverAddress": "http://localhost:9202",
           "communicationType" : "REST"
       },
       {
           "app":"P2P",
           "serverAddress":"http://localhost:9203",
           "sslConfig": {
               "tls": "OFF"
           },
           "communicationType" : "REST"
       }
   ],
   "peer": [
       {
           "url": "http://localhost:9103"
       },
       {
           "url": "http://localhost:9303"
       },
       {
           "url": "http://localhost:9403"
       }
   ],
    "keys": {
        "passwords": [],
        "keyData": [
            {
                "privateKeyPath": "nodeKey.key",
                "publicKeyPath": "nodeKey.pub"
            }
        ]
   },
   "alwaysSendTo": []
}
```
#### Tessera-03
```bash
{
   "mode": "orion",
   "useWhiteList": false,
   "jdbc": {
       "username": "sa",
       "password": "",
       "url": "jdbc:h2:./target/h2/tessera1",
       "autoCreateTables": true
   },
   "serverConfigs":[
       {
           "app":"ThirdParty",
           "serverAddress": "http://localhost:9301",
           "communicationType" : "REST"
       },
       {
           "app":"Q2T",
           "serverAddress": "http://localhost:9302",
           "communicationType" : "REST"
       },
       {
           "app":"P2P",
           "serverAddress":"http://localhost:9303",
           "sslConfig": {
               "tls": "OFF"
           },
           "communicationType" : "REST"
       }
   ],
   "peer": [
       {
           "url": "http://localhost:9103"
       },
       {
           "url": "http://localhost:9203"
       },
       {
           "url": "http://localhost:9403"
       }
   ],
    "keys": {
        "passwords": [],
        "keyData": [
            {
                "privateKeyPath": "nodeKey.key",
                "publicKeyPath": "nodeKey.pub"
            }
        ]
   },
   "alwaysSendTo": []
}
```
#### Tessera-04
```bash
{
   "mode": "orion",
   "useWhiteList": false,
   "jdbc": {
       "username": "sa",
       "password": "",
       "url": "jdbc:h2:./target/h2/tessera1",
       "autoCreateTables": true
   },
   "serverConfigs":[
       {
           "app":"ThirdParty",
           "serverAddress": "http://localhost:9401",
           "communicationType" : "REST"
       },
       {
           "app":"Q2T",
           "serverAddress": "http://localhost:9402",
           "communicationType" : "REST"
       },
       {
           "app":"P2P",
           "serverAddress":"http://localhost:9403",
           "sslConfig": {
               "tls": "OFF"
           },
           "communicationType" : "REST"
       }
   ],
   "peer": [
       {
           "url": "http://localhost:9103"
       },
       {
           "url": "http://localhost:9203"
       },
       {
           "url": "http://localhost:9303"
       }
   ],
    "keys": {
        "passwords": [],
        "keyData": [
            {
                "privateKeyPath": "nodeKey.key",
                "publicKeyPath": "nodeKey.pub"
            }
        ]
   },
   "alwaysSendTo": []
}
```

在配置文件中，指定：

- serverConfigs部分中不同服务器的不同端口号。
- 在对等部分中要发现的Tessera节点的地址。
- 公钥/私钥对的位置。

### 启动Tessera节点
在每个Tessera目录中，启动Tessera指定在上一步中创建的配置文件：

> java -jar ~/tessera-app-21.4.1-app.jar -configfile tessera.conf

### 启动Besu-Node节点

#### Node-1

  > nohup ../../bin/besu --data-path=data --genesis-file=../genesis.json --rpc-http-enabled --rpc-http-api=ETH,NET,IBFT,EEA,PRIV,TRACE --rpc-ws-api=ETH,MINER,IBFT,NET,ADMIN,WEB3,TRACE --host-allowlist="*" --rpc-http-cors-origins="all" --privacy-enabled --privacy-url=http://0.0.0.0:9102 --privacy-public-key-file=Tessera/nodeKey.pub --rpc-http-host=0.0.0.0 --min-gas-price=0 --rpc-http-max-active-connections=1000 &

  开启ws
  > --rpc-ws-api=ETH,MINER,IBFT,NET,ADMIN,WEB3,TRACE

  开启隐私网络
  > --privacy-enabled
  > --privacy-url=http://0.0.0.0:9102
  > --privacy-public-key-file=Tessera/nodeKey.pub
  > --rpc-http-api=ETH,NET,IBFT,EEA,PRIV,TRACE  (JSON-RPC API列表中包括EEA和PRIV，以启用隐私JSON-RPC API方法)

  一旦开启隐私网络,gas要设置为0
  > --min-gas-price=0
  
  设置最大连接数上限
  > --rpc-http-max-active-connections=1000
  
  当节点开始时，将显示枚举URL。复制enode URL，在以下步骤中指定Node-1为引导节点。
  Enode URL:
   > enode://240b21f72ab6db1e05866e6b835ca3a09b915d7f681f435fc6dfddcff391c09ca53f690144c671e0a69a96db7cf3733b7e62d81d36fffc726a2ba2741d6512aa@127.0.0.1:30303

#### Node-2
  > nohup ../../bin/besu --data-path=data --genesis-file=../genesis.json --bootnodes=enode://240b21f72ab6db1e05866e6b835ca3a09b915d7f681f435fc6dfddcff391c09ca53f690144c671e0a69a96db7cf3733b7e62d81d36fffc726a2ba2741d6512aa@127.0.0.1:30303 --p2p-port=30304 --rpc-http-enabled --rpc-http-api=ETH,NET,IBFT,EEA,PRIV,TRACE --rpc-ws-api=ETH,MINER,IBFT,NET,ADMIN,WEB3,TRACE --host-allowlist="*" --rpc-http-cors-origins="all" --rpc-http-port=8546 --privacy-enabled --privacy-url=http://0.0.0.0:9202 --privacy-public-key-file=Tessera/nodeKey.pub --rpc-http-host=0.0.0.0 --min-gas-price=0 & 
    
#### Node-3

  > nohup ../../bin/besu --data-path=data --genesis-file=../genesis.json --bootnodes=enode://240b21f72ab6db1e05866e6b835ca3a09b915d7f681f435fc6dfddcff391c09ca53f690144c671e0a69a96db7cf3733b7e62d81d36fffc726a2ba2741d6512aa@127.0.0.1:30303 --p2p-port=30305 --rpc-http-enabled --rpc-http-api=ETH,NET,IBFT,EEA,PRIV,TRACE --rpc-ws-api=ETH,MINER,IBFT,NET,ADMIN,WEB3,TRACE --host-allowlist="*" --rpc-http-cors-origins="all" --rpc-http-port=8547 --privacy-enabled --privacy-url=http://0.0.0.0:9302 --privacy-public-key-file=Tessera/nodeKey.pub --rpc-http-host=0.0.0.0 --min-gas-price=0 & 
    
#### Node-4

  > nohup ../../bin/besu --data-path=data --genesis-file=../genesis.json --bootnodes=enode://240b21f72ab6db1e05866e6b835ca3a09b915d7f681f435fc6dfddcff391c09ca53f690144c671e0a69a96db7cf3733b7e62d81d36fffc726a2ba2741d6512aa@127.0.0.1:30303 --p2p-port=30306 --rpc-http-enabled --rpc-http-api=ETH,NET,IBFT,EEA,PRIV,TRACE --rpc-ws-api=ETH,MINER,IBFT,NET,ADMIN,WEB3,TRACE --host-allowlist="*" --rpc-http-cors-origins="all" --rpc-http-port=8548 --privacy-enabled --privacy-url=http://0.0.0.0:9402 --privacy-public-key-file=Tessera/nodeKey.pub --rpc-http-host=0.0.0.0 --min-gas-price=0 & 
    
------------------------


