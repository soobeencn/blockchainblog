# 网络构建

***单节点、多节点、源码构建*** 

## 一、开发者网络
Quorum Developer Quickstart使用Hyperledger Besu Docker映像运行由Docker-Compose管理的Besu节点的专用网络。
### 先决条件
- docker & docker-compose

- Nodejs

- Windows

    - Windows Subsystem for Linux2
    - Docker desktop configured to use the WSL2-based engine
- Git Command line

- Curl command line

- A Web browser that supports MetaMask (currently Chrome, Firefox, Opera, and Brave), and has the MetaMask plug-in installed. This tutorial uses screenshots from Brave.

### 创建Docker-compose file
### 运用
创建docker-compose文件和artifacts, 执行命令:

    npx quorum-dev-quickstart
### 运行网络
    ./run.sh
    
脚本构建图像并运行容器。它还将常规节点容器缩放到四个容器，以模拟具有足够对等方进行同步的网络
当进程结束时，它会列出正在运行的服务：
    
    *************************************
    Quorum Dev Quickstart 
    *************************************
    Setting up the index patterns in kibana .....
    ----------------------------------
    List endpoints and services
    ----------------------------------
    JSON-RPC HTTP service endpoint      : http://localhost:8545
    JSON-RPC WebSocket service endpoint : ws://localhost:8546
    Web block explorer address          : http://localhost:25000/
    Prometheus address                  : http://localhost:9090/graph
    Grafana address                     : http://localhost:3000/d/XE4V0WGZz/besu-overview?orgId=1&refresh=10s&from=now-30m&to=now&var-system=All
    Collated logs using Kibana endpoint : http://localhost:5601/app/kibana#/discover
    
    For more information on the endpoints and services, refer to README.md in the installation directory.
    ****************************************************************


- 使用JSON-RPC HTTP服务端点从您的Dapp或MetaMask等加密货币钱包访问RPC节点服务。
- 使用JSON-RPC WebSocket服务端点从Dapp访问Web套接字节点服务。
- 使用Web块资源管理器地址显示块资源管理器Web应用程序。通过在网页浏览器中输入URL查看块资源管理器。
- 使用普罗米修斯地址访问普罗米修斯仪表板。
- 使用格拉法纳地址访问格拉法纳仪表板。
- 使用Kibana日志地址访问Kibana中的日志。

要再次显示端点列表, 请运行:

    ./list.sh
    
### 区块探索(区块浏览器)

### 使用普罗米修斯和格拉法纳监控节点

- 普罗米修斯 https://prometheus.io/docs/introduction/overview/
- 格拉法纳   https://grafana.com/docs/

### 运行JSON-RPC请求
You can run RPC requests on rpcnode, the node exposed to the host to listen for requests. This tutorial uses cURL to make JSON-RPC requests.

示例如下:
#### 请求节点版本
从主机shell运行以下命令:

    curl -X POST --data '{"jsonrpc":"2.0","method":"web3_clientVersion","params":[],"id":1}' http://localhost:8545
    
结果:返回指定客户端版本

    {
       "jsonrpc" : "2.0",
       "id" : 1,
       "result" : "besu/<version number>"
    }
    
如果返回了上述结果, 说明节点正在运行中.

这样,我们就可以使用此节点, 进行一些简单的交互, 或者我们可以通过MetaMask创建一笔交易.

#### 计算节点数 count the peers
Peers are the number of other nodes connected to the RPC node.

获取peer节点数量通过使用: **net_peerCount:**

    curl -X POST --data '{"jsonrpc":"2.0","method":"net_peerCount","params":[],"id":1}' http://localhost:8545
    
The result indicates that there are four peers:

    {
      "jsonrpc" : "2.0",
      "id" : 1,
      "result" : "0x4"
    }

#### 请求最新的区块高度(block number)
调用 **eth_blockNumber** 获取最新区块高度:

    curl -X POST --data '{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":1}' http://localhost:8545
    
结果:

    {
      "jsonrpc" : "2.0",
      "id" : 1,
      "result" : "0x8b8"
    }
The hexadecimal value 0x8b8 translates to 2232 in decimal, the number of blocks so far.

### 使用MetaMask创建一笔交易
在发送事务之前，您需要创建一个帐户或使用在此专用测试网络生成期间创建的以下帐户之一。

**Warning**

不能使用以下账户在以太坊主网or任何公网进行交易测试
    
The private keys are displayed here which means the accounts are not secure.

--------------------------------
- Account 1 (Miner Coinbase Account)
- Address: 0xfe3b557e8fb62b89f4916b721be55ceb828dbd73
- Private key : 0x8f2a55949038a9610f50fb23b5883af3b4ecb3c3bb792cbcefbd1542c692be63
- Initial balance : 0xad78ebc5ac6200000 (200000000000000000000 in decimal)

----------------------------
- Account 2
- Address: 0x627306090abaB3A6e1400e9345bC60c78a8BEf57
- Private key : 0xc87509a1c067bbde78beb793e6fa76530b6382a4c0241e5e4a9ec0a0f44dc0d3
- Initial balance : 0x90000000000000000000000 (2785365088392105618523029504 in decimal)

-------------------------------
- Account 3
- Address: 0xf17f52151EbEF6C7334FAD080c5704D77216b732
- Private key : 0xae6ae8e5ccbfb04590405997ee2d52d2b330726137b875053c36d94e974d162f
- Initial balance : 0x90000000000000000000000 (2785365088392105618523029504 in decimal)

------------------------

之后我们登录到MetaMask, 连接自定义RPC测试节点:

- 1.在MetaMask网络列表中, 选择Custom RPC
- 2.当你开始了新的private network, 在新的RPC URL下, 输入JSON-RPC HTTP 服务节点, 保存配置, 然后返回到MetaMask主要界面, 这时你的网络已经连接到自定义配置的RPC节点.

通过正确的方式, 导入上述三个账户的私钥(private key)

当你导入成功后, 创建一个新的账户(余额为0), 然后开始发起一笔交易, 几秒钟后, 刷新浏览器, 显示交易成功

备注: MetaMask常规操作可以参考  
- _https://metamask.zendesk.com/hc/en-us/articles/360015289452-Creating-Additional-MetaMask-Wallets-New-UI-_

### 使用智能合约
#### 先决条件
- Node.js v6+ LTS and npm (comes with Node)

- 导入一个账户到MetaMask, 通过使用这个私钥 **0xc87509a1c067bbde78beb793e6fa76530b6382a4c0241e5e4a9ec0a0f44dc0d3**, 以下操作要用到这个账户.

#### 安装Truffle

    npm install -g truffle

备注: **npm** 在linux中使用需要 **sudo** 权限

### 停止并重启私有网络

关闭私有网络,不删除容器:
```    
./stop.sh
```

重新启动私有网络:
```
./resume.sh
```

### 停止私有网络并移除容器

关闭专用网络并删除从运行示例网络创建的所有容器和图像:
```
./remove.sh
```

-----------------------------

## 二、源码构建

### 先决条件 
Java 11+ 
对于Mac安装：MacOS High Sierra 10.13或更高版本
注意：  Besu当前仅在Windows的64位版本上受支持，并且需要64位版本的JDK / JRE。我们建议您还删除所有32位JDK / JRE安装。

### 依赖

从源代码构建只有一个依赖项-需要libsodium版本1.0.16或更高 版本 才能构建Besu并运行测试。

如果不打算运行测试，则可以跳过此步骤。

要安装，请按照此处的说明进行操作，或在MacOS或Ubuntu上键入以下命令。

Mac OS：
> $ brew install libsodium 

Ubuntu（16.04 LTS）：
> $ apt install libsodium18 

Ubuntu（18.04 LTS）：
> $ apt install libsodium23 

Ubuntu（14.04 LTS）：
您可以在此处找到合适的软件包（v1.0.16-0）文件：
https : //launchpad.net/ubuntu/+source/libsodium
https://launchpad.net/~phoerious/+archive/ubuntu/keepassxc/+sourcepub/8814980/+listing-archive-extra


在Linux上可能需要`sudo`命令，如果屏幕提示您输入密码，请输入密码并按Enter。

### 快速开始
> git clone --recursive https://github.com/hyperledger/besu

>  cd besu 

> ./gradlew build

> ./gradlew integrationTest LTS

#### 结帐源代码
> git clone --recursive git@github.com:hyperledger/besu.git

或者

> git clone --recursive https://github.com/hyperledger/besu

#### 查看可用的任务 
要查看所有可用的gradle任务，请执行以下操作：

> cd besu

> ./gradlew tasks  

#### 从源代码构建

注意：在Windows上，要运行 gradlew，您必须将 JAVA_HOME 系统变量设置为Java安装目录。例如： JAVA_HOME = C:\Program Files\Java\jdk1.8.0_181

##### 构建发行版二进制文件： 

> cd besu

> ./gradlew installDist



运行Besu：

> cd build\install\besu
> 
> ./bin/besu --help

运行开发人员构建
使用以下默认选项构建和运行Besu：

> ./gradlew installDist

默认情况下，它将所有持久性数据存储在中build/install/besu。

为Besu执行设置自定义CLI参数：

> cd build/install/besu    

> ./bin/besu --discovery-enabled=false --data-path=/tmp/besutmp

## 三、专用网络

### IBFT2.0 共识网络启动
本文档仅针对IBFT2.0网络，节点间以静态节点的方式配置对等节点。

服务节点分配如下:

| 节点名称 | 节点ip | BESU_HOME目录 | DATE目录 | p2p端口 |
| --- | --- | --- | --- | --- |
| node1 | 192.168.1.171 | /opt/besu/besu-IBFT-Network | /opt/besu/besu-IBFT-Network/data | 30303 |
| node2 | 192.168.1.173 | /opt/besu/besu-IBFT-Network | /opt/besu/besu-IBFT-Network/data | 30303 |
| node3 | 192.168.1.174 | /opt/besu/besu-IBFT-Network | /opt/besu/besu-IBFT-Network/data | 30303 |
| node4 | 192.168.1.175 | /opt/besu/besu-IBFT-Network | /opt/besu/besu-IBFT-Network/data | 30303 |


### 1.安装besu
四个节点皆执行同样的步骤。
#### 1).安装jdk
```bash
wget https://download.java.net/openjdk/jdk11/ri/openjdk-11+28_linux-x64_bin.tar.gz
tar zxf openjdk-11+28_linux-x64_bin.tar.gz
mv jdk-11/ /usr/local/
```
#### 2).下载besu并解压
```bash
wget https://hyperledger.jfrog.io/artifactory/besu-binaries/besu/21.1.7/besu-21.1.7.zip
mkdir /opt/besu
mv besu-21.1.7.zip /opt/besu
unzip besu-21.1.7.zip
cp -r besu-21.1.7 besu-IBFT-Network
```
#### 3).修改besu脚本，添加JAVA_HOME
```bash
cd /opt/besu/besu-IBFT-Network
cat besu
#!/usr/bin/env sh
......
export JAVA_HOME=/usr/local/jdk-11
```
#### 4).测试besu
![image.png](https://cdn.nlark.com/yuque/0/2021/png/2560185/1623318806536-657bacb1-c564-41cf-aac7-5223375f1dd0.png#align=left&display=inline&height=185&margin=%5Bobject%20Object%5D&name=image.png&originHeight=369&originWidth=874&size=39168&status=done&style=none&width=437)

### 2.生成密钥和创世文件
#### 参考文档：[https://besu.hyperledger.org/en/stable/Tutorials/Private-Network/Create-IBFT-Network/](https://besu.hyperledger.org/en/stable/Tutorials/Private-Network/Create-IBFT-Network/)
只在其中一个节点执行，如本示例中的192.168.1.171，执行完成后把相关文件复制到其它节点即可。
#### 1).创建IBFT配置文件
```bash
#创建目录
mkdir {node1,node2,node3,node4}/data
#创建密钥IBFT配置文件，配置文件名为ibftConfigFile.json 
mkdir config
cd config
cat ibftConfigFile.json 
{
 "genesis": {
   "config": {
      "chainId": 2021,
      "muirglacierblock": 0,
      "ibft2": {
        "blockperiodseconds": 2,
        "epochlength": 30000,
        "requesttimeoutseconds": 4
      }
    },
    "nonce": "0x0",
    "timestamp": "0x58ee40ba",
    "gasLimit": "0x1fffffffffffff",
    "difficulty": "0x1",
    "mixHash": "0x63746963616c2062797a616e74696e65206661756c7420746f6c6572616e6365",
    "coinbase": "0x0000000000000000000000000000000000000000",
    "alloc": {}
 },
 "blockchain": {
   "nodes": {
     "generate": true,
       "count": 4
   }
 }
}
```

- `blockperiodseconds` ： 最小区块时间，以秒为单位。
- `epochlength` ：重置所有投票后的块数。
- `requesttimeoutseconds` ： 回合更改前每个共识回合的超时时间，以秒为单位。
- `blockreward`  ：可选择奖励金额以奖励受益人。默认为零 (0)。可以指定为十六进制（带有 0x 前缀）或十进制字符串值。如果设置，则网络上的所有节点必须使用相同的值。
- `miningbeneficiary`  ：blockreward的可选受益人。默认为提议区块的验证者。如果设置，则网络上的所有节点必须使用相同的受益人。
- `extraData`  : `RLP([32 bytes Vanity, List<Validators>, No Vote, Round=Int(0), 0 Seals])`.


#### 2).生成密钥和创世文件
> bash

##### a.生成证书和密钥
##### b.operator: besu子命令
##### c.generate-blockchain-config: besu用于生成IBFT2.0配置文件
> ../bin/besu operator generate-blockchain-config --config-file=ibftConfigFile.json --to=networkFiles --private-key-file-name=key

##### d.生成文件目录如下所示
- genesis.json: 包含extradata信息的四个节点的创世文件
- key: 私钥，该文件要放在besu data目录下，如过检测不到该文件，besu启动时也会自动生成
- key.pub: 公钥，公钥是节点访问链接的重要组成部分

> tree  networkFiles/


    networkFiles/
    ├── genesis.json
    └── keys
        ├── 0x260051031d75665c6325b0c0dc6905e2f2b03a3b
        │   ├── key
        │   └── key.pub
        ├── 0x307ff04a0417d801c47ee020efe9a1e1f618916f
        │   ├── key
        │   └── key.pub
        ├── 0x8ab4324942ef13976191533558b083f5fca88913
        │   ├── key
        │   └── key.pub
        └── 0x9b47542fa40745a6257fdc79209c07e9166126e9
            ├── key
            └── key.pub
 
##### e.四个文件分布复制到四个node目录下
> cd networkFiles

> cp 0x260051031d75665c6325b0c0dc6905e2f2b03a3b/key* /opt/besu/besu-IBFT-Network/node1/data/

> cp 0x8ab4324942ef13976191533558b083f5fca88913/key* /opt/besu/besu-IBFT-Network/node2/data/

> cp 0x307ff04a0417d801c47ee020efe9a1e1f618916f/key* /opt/besu/besu-IBFT-Network/node3/data/

> cp 0x9b47542fa40745a6257fdc79209c07e9166126e9/key* /opt/besu/besu-IBFT-Network/node4/data/

##### f.复制genesis.json文件至config目录
> cp genesis.json /opt/besu/besu-IBFT-Network/config
> tree .

    .
    ├── node1
    │   ├── data
    │   │   ├── key
    │   │   └── key.pub
    ├── node2
    │   ├── data
    │   │   ├── key
    │   │   └── key.pub
    ├── node3
    │   ├── data
    │   │   ├── key
    │   │   └── key.pub
    ├── node4
    │   ├── data
    │   │   ├── key
    │   │   └── key.pub
    ......


### 3.配置privacy enabled network
#### 1).安装tessera
tessera安装简单，直接下载tessera-app jar文件即可

> wget https://oss.sonatype.org/service/local/repositories/releases/content/net/consensys/quorum/tessera/tessera-app/21.4.1/tessera-app-21.4.1-app.jar

> cp tessera-app-21.4.1-app.jar /opt/besu/besu-IBFT-Network/lib/
  ##### 创建命令别名
> alias tessera="/usr/local/jdk-11/bin/java -jar /opt/besu/tessera/tessera-app-21.4.1-app.jar"

#### 2).创建tessera目录，并生成Tessera keys
```bash
mkdir -p {node1,node2,node3,node4}/tessera
for i in node1 node2 node3 node4
do
cd $i/tessera
tessera -keygen -filename nodekey
done

cp -r node1/tessera /opt/besu/besu-IBFT-Network/
```
生成Tessera keys时，可以配置私钥密码，配置时，注意保存。
#### 3).创建tessera配置文件

文件名称: tessera.conf, 可以配置私钥密码, 配置时, 注意保存.

```bash
{
   "mode": "orion",
   "useWhiteList": false,
   "jdbc": {
       "username": "sa",
       "password": "BMZV0920480_750daq",
       "url": "jdbc:h2:/opt/besu/besu-IBFT-Network/tessera/target/h2/tessera1",
       "autoCreateTables": true
   },
   "serverConfigs":[
       {
           "app":"ThirdParty",
           "serverAddress": "http://192.168.1.171:19101",
           "communicationType" : "REST"
       },
       {
           "app":"Q2T",
           "serverAddress": "http://192.168.1.171:19102",
           "communicationType" : "REST"
       },
       {
           "app":"P2P",
           "serverAddress":"http://192.168.1.171:19103",
           "sslConfig": {
               "tls": "OFF"
           },
           "communicationType" : "REST"
       }
   ],
   "peer": [
       {
           "url": "http://192.168.1.173:19103"
       },
       {
           "url": "http://192.168.1.174:19103"
       },
       {
           "url": "http://192.168.1.175:19103"
       }
   ],
    "keys": {
        "passwordFile": "/opt/besu/besu-IBFT-Network/tessera/password.txt",
        "keyData": [
            {
                "privateKeyPath": "/opt/besu/besu-IBFT-Network/tessera/nodekey.key",
                "publicKeyPath": "/opt/besu/besu-IBFT-Network/tessera/nodekey.pub"
            }
        ]
   },
   "alwaysSendTo": []
}
```

serverConfigs：配置本机tessera server的配置信息
peer：配置要发现的tessera节点地址
key：配置tessera公私钥以及私钥密码，私钥密码存储在文件中，通过passwordFile配置项指定

复制配置文件至三个节点目录下, 并修改ip配置.
修改说明:
- 1.把serverconfigs下的192.168.1.171更改为对应节点服务器的ip地址
- 2.把peer配置下对应节点服务器的ip地址改为 192.168.1.171
- 3.password.txt根据具体情况添加, 注意添加目录要和tessera.conf中的一致

```bash
cd /opt/besu/besu-IBFT-Network/tessera

cp tessera.conf /opt/besu/besu-IBFT-Network/node2/tessera/
sed -i "s;192.168.1.171;192.168.1.173;" /opt/besu/besu-IBFT-Network/node2/tessera/tessera.conf
sed -i "s;\"url\": \"http://192.168.1.173:19103\";\"url\": \"http://192.168.1.171:19103\";" /opt/besu/besu-IBFT-Network/node2/tessera/tessera.conf


cp tessera.conf /opt/besu/besu-IBFT-Network/node3/tessera/
sed -i "s;192.168.1.171;192.168.1.174;" /opt/besu/besu-IBFT-Network/node3/tessera/tessera.conf
sed -i "s;\"url\": \"http://192.168.1.174:19103\";\"url\": \"http://192.168.1.171:19103\";" /opt/besu/besu-IBFT-Network/node3/tessera/tessera.conf

cp tessera.conf /opt/besu/besu-IBFT-Network/node4/tessera/
sed -i "s;192.168.1.171;192.168.1.175;" /opt/besu/besu-IBFT-Network/node4/tessera/tessera.conf
sed -i "s;\"url\": \"http://192.168.1.175:19103\";\"url\": \"http://192.168.1.171:19103\";" /opt/besu/besu-IBFT-Network/node4/tessera/tessera.conf

```


### 4.启动节点
### 1).创建配置文件config.toml
```bash
# Valid TOML config file
data-path="/opt/besu/besu-IBFT-Network/data"
logging="INFO"
host-whitelist=["*"]

# peer节点相关配置，
p2p-host="192.168.1.171"
p2p-port=30303
max-peers=42

# HTTP JSON-RPC服务相关配置
rpc-http-host="192.168.1.171"
rpc-http-port=18548

rpc-ws-host="192.168.1.171"
rpc-ws-port=18501

rpc-http-enabled=true
rpc-http-api=["ETH","NET","IBFT","EEA","PRIV"]
rpc-http-cors-origins=["all"]

# privacy nework相关配置
privacy-enabled=true
privacy-url="http://192.168.1.171:19102"
privacy-public-key-file="/opt/besu/besu-IBFT-Network/tessera/nodekey.pub"
min-gas-price=0

# 监控信息相关配置
metrics-push-enabled=true
metrics-push-port=9091
metrics-push-host="192.168.1.171"

# Chain
genesis-file="/opt/besu/besu-IBFT-Network/config/genesis.json" # Path to the custom genesis file

# Mining
miner-enabled=false
miner-coinbase="0xfe3b557e8fb62b89f4916b721be55ceb828dbd73"
```
#### 复制到node目录下
```bash
cd /opt/besu/besu-IBFT-Network
mkdir node2/config
cp config/config.toml node2
sed "s;192.168.1.171;192.168.1.173;" node2/config/config.toml
cp config/genesis.json node2/config/

mkdir node3/config
cp config.toml node3
sed "s;192.168.1.171;192.168.1.174;" node3/config/config.toml
cp config/genesis.json node3/config/

mkdir node4/config
cp config.toml node4
sed "s;192.168.1.171;192.168.1.175;" node4/config/config.toml
cp config/genesis.json node4/config/
```

### 2).根据key.pub创建static-nodes.json文件
static-nodes.json 文件内容示例：
```bash
["enode://cdb2489ff4c7487fd1ff4ff89b2db0570cd60dec95a0ab1b4e020a2b54f3edf23da42403cea8e2fa7b1209046894a45b1e52a225c9280afebb44e6c0e3e03c04@192.168.1.171:30303",
"enode://f86f066527aba9fb1ea9440f6b8abf44f5dd4db19cbcd54b2441f31d8f5fb500d14fe8705bd6a70708bffaaea140e5bc1eef395754bc8b66087bc476694b4bf6@192.168.1.173:30303",
"enode://31576f554f73280d625296f53a583ac9a56be26c3b5196003fc92d5a76b77fb377d24ef6a0c55f06c266e46648e1538c5c852d20b2c833a611290f138d5bff7d@192.168.1.174:30303",
"enode://29cbe5f59aa603ff74136b304984126ba4bbcefd8e394f762b59c2311a57727f1a4d5c0ec3371b876be23af4afba056e7a18516da78e43545eae94952c132543@192.168.1.175:30303"
]
```
说明：

- cdb2489ff4c7487fd1ff4ff89b2db0570cd60dec95a0ab1b4e020a2b54f3edf23da42403cea8e2fa7b1209046894a45b1e52a225c9280afebb44e6c0e3e03c04：这是通过besu opretor生成的节点公钥，注意，这里不包含“0x”；
- 192.168.1.171:30303：p2p配置项中，p2p-host和p2p-port的内容；
- static-nodes.json文件要存放在data目录下，即配置文件中data-path配置的目录下

### 3).创建启动脚本
在BESU_HOME目录下创建启动脚本start.sh。

```bash
#!/bin/bash

BESU_HOME=/opt/besu/besu-IBFT-Network
TESS_HOME=/opt/besu/besu-IBFT-Network/tessera
export JAVA_HOME=/usr/local/jdk-11

which jps || echo "jps未安装，请安装java-1.8.0-openjdk-devel;如使用二进制安装java，请检查该安装包是否已包含jps命令"

start_Tesse() {
[ ! -d logs ] && mkdir logs
#tessera-app-21.4.1-app.jar需要先下载，并复制到$BESU_HOME/lib目录下
nohup /usr/local/jdk-11/bin/java -jar $BESU_HOME/lib/tessera-app-21.4.1-app.jar --configfile Tessera/tessera.conf >>$BESU_HOME/logs/tessera.log 2>&1 &
}

stop_tesse() {
PID=$(jps -lmv | grep tessera-app-21.4.1-app.jar | awk '{print $1}')
[ ! $PID ] && "PID不存在，请检查服务是否已经启动" && exit 1
kill $PID
[ $? -eq 0 ] && echo "节点已停止"
}

start() {
start_Tesse
nohup $BESU_HOME/bin/besu --config-file=$BESU_HOME/config/config.toml >>$BESU_HOME/logs/besu.log 2>&1 &
for i in {1..10};do
sleep 2
ENODE_URL=$(grep "Enode URL" $BESU_HOME/logs/besu.log  | awk '{print $NF}')
[ ! $ENODE_URL ] && continue
break
done
echo "$ENODE_URL"
}

stop() {
#PID=$(ps -elf | grep "$BESU_HOME/config" | grep -v grep | awk '{print $4}')
PID=$(jps -lmv  | grep org.hyperledger.besu.Besu | grep $BESU_HOME | awk '{print $1}')
[ ! $PID ] && "PID不存在，请检查服务是否已经启动" && exit 1
kill $PID
[ $? -eq 0 ] && echo "节点已停止"
}
$1
```
复制启动脚本至节点目录下：

    cp start.sh node2/
    cp start.sh node3/
    cp start.sh node4/

### 4).复制配置目录至其它节点
```bash
#复制node2至192.168.1.173
scp -r /opt/besu/besu-IBFT-Network/node2/* 192.168.1.173:/opt/besu/besu-IBFT-Network
scp 
#复制node3至192.168.1.174
scp -r /opt/besu/besu-IBFT-Network/node3/* 192.168.1.174:/opt/besu/besu-IBFT-Network
#复制node4至192.168.1.175
scp -r /opt/besu/besu-IBFT-Network/node4/* 192.168.1.175:/opt/besu/besu-IBFT-Network
```
### 5).各节点使用启动脚本启动服务
```bash
cd /opt/besu/besu-IBFT-Network/
./start.sh start
```
启动后，查看logs目录下的tessera.log的日志信息以及besu.logs的日志信息，确认启动是否完成。

---------------------

### Clique共识网络启动

本文档仅针对clique网络，节点间以静态节点的方式配置对等节点。
服务器规划说明：

| 节点名称 | 节点ip | BESU_HOME目录 | DATE目录 | p2p端口 |
| --- | --- | --- | --- | --- |
| node1 | 192.168.1.171 | /opt/besu/besu-clique-network | /opt/besu/besu-clique-network/data | 30304 |
| node2 | 192.168.1.173 | /opt/besu/besu-clique-network | /opt/besu/besu-clique-network/data | 30304 |
| node3 | 192.168.1.174 | /opt/besu/besu-clique-network | /opt/besu/besu-clique-network/data | 30304 |
| node4 | 192.168.1.175 | /opt/besu/besu-clique-network | /opt/besu/besu-clique-network/data | 30304 |

### 一、安装besu
四个节点皆执行同样的步骤。
1、安装jdk
```bash
wget https://download.java.net/openjdk/jdk11/ri/openjdk-11+28_linux-x64_bin.tar.gz
tar zxf openjdk-11+28_linux-x64_bin.tar.gz
mv jdk-11/ /usr/local/
```
2、下载besu并解压
```bash
wget https://hyperledger.jfrog.io/artifactory/besu-binaries/besu/21.1.7/besu-21.1.7.zip
mkdir /opt/besu
mv besu-21.1.7.zip /opt/besu
unzip besu-21.1.7.zip
cp -r besu-21.1.7 besu-clique-network
```
3、修改besu脚本，添加JAVA_HOME
```bash
cd /opt/besu/besu-clique-network
cat besu
#!/usr/bin/env sh
......
export JAVA_HOME=/usr/local/jdk-11
```
4、测试besu
![image.png](https://cdn.nlark.com/yuque/0/2021/png/2560185/1623318806536-657bacb1-c564-41cf-aac7-5223375f1dd0.png#align=left&display=inline&height=185&margin=%5Bobject%20Object%5D&name=image.png&originHeight=369&originWidth=874&size=39168&status=done&style=none&width=437)


### 二、配置privacy enabled network
#### 1、安装tessera
tessera安装简单，直接下载tessera-app jar文件即可。该步骤四个节点都要执行
```bash
#下载
wget https://oss.sonatype.org/service/local/repositories/releases/content/net/consensys/quorum/tessera/tessera-app/21.4.1/tessera-app-21.4.1-app.jar
cp tessera-app-21.4.1-app.jar /opt/besu/besu-clique-network/lib/
#创建命令别名
alias tessera="/usr/local/jdk-11/bin/java -jar /opt/besu/besu-clique-network/lib//tessera-app-21.4.1-app.jar"
```
#### 2、创建tessera目录，并生成Tessera keys
```bash
mkdir -p {node1,node2,node3,node4}/tessera
for i in node1 node2 node3 node4
do
cd /opt/besu/besu-clique-network/$i/tessera
tessera -keygen -filename nodekey
done
cp -r node1/tessera /opt/besu/besu-clique-network/
```
生成Tessera keys时，可以配置私钥密码，配置时，注意保存。
#### 3、创建tessera配置文件
文件名称：tessera.conf ；文件所在目录：/opt/besu/besu-clique-network/tessera
```bash
{
   "mode": "orion",
   "useWhiteList": false,
   "jdbc": {
       "username": "sa",
       "password": "BMZV0920480_750daq",
       "url": "jdbc:h2:/opt/besu/besu-clique-network/tessera/target/h2/tessera1",
       "autoCreateTables": true
   },
   "serverConfigs":[
       {
           "app":"ThirdParty",
           "serverAddress": "http://192.168.1.171:19111",
           "communicationType" : "REST"
       },
       {
           "app":"Q2T",
           "serverAddress": "http://192.168.1.171:19112",
           "communicationType" : "REST"
       },
       {
           "app":"P2P",
           "serverAddress":"http://192.168.1.171:19113",
           "sslConfig": {
               "tls": "OFF"
           },
           "communicationType" : "REST"
       }
   ],
   "peer": [
       {
           "url": "http://192.168.1.173:19113"
       },
       {
           "url": "http://192.168.1.174:19113"
       },
       {
           "url": "http://192.168.1.175:19113"
       }
   ],
    "keys": {
        "passwordFile": "/opt/besu/besu-clique-network/tessera/password.txt",
        "keyData": [
            {
                "privateKeyPath": "/opt/besu/besu-clique-network/tessera/nodekey.key",
                "publicKeyPath": "/opt/besu/besu-clique-network/tessera/nodekey.pub"
            }
        ]
   },
   "alwaysSendTo": []
}
```
serverConfigs：配置本机tessera server的配置信息
peer：配置要发现的tessera节点地址
key：配置tessera公私钥以及私钥密码，私钥密码存储在文件中，通过passwordFile配置项指定


复制配置文件至三个节点目录下，并修改ip配置。
修改说明：
1、把serverconfigs下的192.168.1.171换成对应节点服务器的ip地址
2、把peer配置下对应节点服务器的ip地址改为192.168.1.171
3、password.txt根据具体情况添加，注意添加目录要和tessera.conf中的一致
```bash
cd /opt/besu/besu-clique-network/tessera
cp tessera.conf password.txt /opt/besu/besu-clique-network/node2/tessera/
sed -i "s;192.168.1.171;192.168.1.173;" /opt/besu/besu-clique-network/node2/tessera/tessera.conf
sed -i "s;\"url\": \"http://192.168.1.173:19113\";\"url\": \"http://192.168.1.171:19113\";" /opt/besu/besu-clique-network/node2/tessera/tessera.conf

cp tessera.conf password.txt /opt/besu/besu-clique-network/node3/tessera/
sed -i "s;192.168.1.171;192.168.1.174;" /opt/besu/besu-clique-network/node3/tessera/tessera.conf
sed -i "s;\"url\": \"http://192.168.1.174:19113\";\"url\": \"http://192.168.1.171:19113\";" /opt/besu/besu-clique-network/node3/tessera/tessera.conf

cp tessera.conf password.txt /opt/besu/besu-clique-network/node4/tessera/
sed -i "s;192.168.1.171;192.168.1.175;" /opt/besu/besu-clique-network/node4/tessera/tessera.conf
sed -i "s;\"url\": \"http://192.168.1.175:19113\";\"url\": \"http://192.168.1.171:19113\";" /opt/besu/besu-clique-network/node4/tessera/tessera.conf


```
### 三、配置创世文件
#### 1、生成key、public-key以及nodeAddress
```bash
mkdir -p {node2,node3,node4}/{data,config}
mkdir data config
for i in node2 node3 node4; do 
bin/besu --data-path=$i/data public-key export-address --to=$i/data/cliqueAddress
bin/besu --node-private-key-file=$i/data/key  public-key export --to=$i/data/public-key
done
```

- key：私钥
- cliqueAddress：extradata中配置的节点地址
- public-key：公钥，enodeURL中使用public-key作为节点ID
#### 2、生成genesis-file: cliqueGenesis.json
生成目录：$BESU_HOME/config
生成文件名称：cliqueGenesis.json
```bash
{
  "config":{
    "chainId":1337,
    "constantinoplefixblock": 0,
    "clique":{
      "blockperiodseconds":15,
      "epochlength":30000
    }
  },
  "coinbase":"0x0000000000000000000000000000000000000000",
  "difficulty":"0x1",
          "extraData":"0x0000000000000000000000000000000000000000000000000000000000000000<Node Address>0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
  "gasLimit":"0xa00000",
  "mixHash":"0x0000000000000000000000000000000000000000000000000000000000000000",
  "nonce":"0x0",
  "timestamp":"0x5c51a607",
  "alloc": {}
}
```

- blockperiodseconds：区块时间，以秒为单位
- epochlength：重置所有投票后的块数
- extraData：虚数据占用前 32 个字节，然后是初始签名者，即本示例中的“<Node Address>”。签名者可以是1个，也可以是多个，多个地址时，把各个签名者的地址串联配置在此处，注意不要占用后面的130个字节。
- Node Address地址通过命令bin/besu --data-path=data public-key export-address --to=data/cliqueAddress获取。其中cliqueAddress的内容即为Node address（不包含0x）



配置文件如下所示：
```bash
{
  "config":{
    "chainId":1337,
    "constantinoplefixblock": 0,
    "clique":{
      "blockperiodseconds":15,
      "epochlength":30000
    }
  },
  "coinbase":"0x0000000000000000000000000000000000000000",
  "difficulty":"0x1",
  "extraData":"0x0000000000000000000000000000000000000000000000000000000000000000100f42b8a38904268a5c2447ec2b14bc9c37b68b9b7d1d61ac3940f151c5f59ea0c3ecbf36f720a07623627cdb943acb266049cf6109ea5c653c73c70000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
  "gasLimit":"0xa00000",
  "mixHash":"0x0000000000000000000000000000000000000000000000000000000000000000",
  "nonce":"0x0",
  "timestamp":"0x5c51a607",
  "alloc": {}
}
```
三个签名者的cliqueAddress

```bash
cat node1/data/cliqueAddress 
0x100f42b8a38904268a5c2447ec2b14bc9c37b68b

cat node2/data/cliqueAddress 
0x9b7d1d61ac3940f151c5f59ea0c3ecbf36f720a0

cat node3/data/cliqueAddress  
0x7623627cdb943acb266049cf6109ea5c653c73c7
```
#### 3、复制cliqueGenesis.json至三个节点目录下
```bash
cp config/cliqueGenesis.json node2/config
cp config/cliqueGenesis.json node3/config
cp config/cliqueGenesis.json node4/config
```


### 四、启动节点
### 1、创建配置文件config.toml
```bash
# Valid TOML config file
data-path="/opt/besu/besu-clique-network/data" # Path
node-private-key-file="/opt/besu/besu-clique-network/data/key"
logging="INFO"
host-whitelist=["*"]
network-id=337

# Network
#bootnodes=["enode://a0913c4ad51613ca15bef3133817dfe4acd0da2094e3e2925108926d2061b643e31069eec81522c8bfc991655a933a1afdd0d11a6dc30b0661287d33d1350227@192.168.1.171:30304","enode://02018a6fc5fccf3ab9e62ab668d36834807cf1463ad52934409fbdb1bfb28d2b14b91feb7ea662d7938e880210606af541a97cbf378b81c912a7b0c17ccb35dc@192.168.1.173:30304","enode://a58c6c6226ef9b2f0a7e404105d33fe614c28b9aade3af33908460ce87333c60e3b7e16315247677f4d22a535e414f88ef1ada7eaa066935b295befb85ffdc07@192.168.1.174:30304"]

#根据节点ip和端口修改
p2p-host="192.168.1.171"
p2p-port=30304
max-peers=42

#根据节点ip和端口修改
rpc-http-host="192.168.1.171"
rpc-http-port=18549

#根据节点ip和端口修改
rpc-ws-host="192.168.1.171"
rpc-ws-port=18502

rpc-http-enabled=true
rpc-http-api=["ETH","NET","CLIQUE","EEA","PRIV"]
rpc-http-cors-origins=["all"]
privacy-enabled=true
#根据节点ip和端口修改
privacy-url="http://192.168.1.171:19112"
privacy-public-key-file="/opt/besu/besu-clique-network/tessera/nodekey.pub"
min-gas-price=0

#这里配置的是prometheus的地址，也可以是push-gateway的地址。
metrics-push-enabled=true
metrics-push-port=9091
metrics-push-host="192.168.1.170"

# 创世文件路径
genesis-file="/opt/besu/besu-clique-network/config/cliqueGenesis.json" 

# Mining
miner-enabled=false
miner-coinbase="0xfe3b557e8fb62b89f4916b721be55ceb828dbd73"

#静态节点需关闭自动发现
discovery-enabled=false
```
复制到node目录下
```bash
cd /opt/besu/besu-clique-network
cp config/config.toml node2
sed -i "s;192.168.1.171;192.168.1.173;" node2/config/config.toml

cp config.toml node3
sed -i "s;192.168.1.171;192.168.1.174;" node3/config/config.toml

cp config.toml node4
sed -i "s;192.168.1.171;192.168.1.175;" node4/config/config.toml
```

### 2、根据public-key创建static-nodes.json文件
查看各个节点publick-key内容，使用enode://<节点ID>@<p2p-host>:<p2p-port>构成一个节点链接。其中节点ID通过使用命令“bin/besu --node-private-key-file=data/key  public-key export --to=data/public-key”生成的public-key。p2p-host、p2p-port则在配置文件config.toml中指定。
static-nodes.json 文件内容示例：
```bash
["enode://a0913c4ad51613ca15bef3133817dfe4acd0da2094e3e2925108926d2061b643e31069eec81522c8bfc991655a933a1afdd0d11a6dc30b0661287d33d1350227@192.168.1.171:30304","enode://02018a6fc5fccf3ab9e62ab668d36834807cf1463ad52934409fbdb1bfb28d2b14b91feb7ea662d7938e880210606af541a97cbf378b81c912a7b0c17ccb35dc@192.168.1.173:30304","enode://a58c6c6226ef9b2f0a7e404105d33fe614c28b9aade3af33908460ce87333c60e3b7e16315247677f4d22a535e414f88ef1ada7eaa066935b295befb85ffdc07@192.168.1.174:30304",
"enode://145709b9b94d9c9b86ba4070873fcee3e84034726089348b1b3cb6d4c51e0f4667a56abe369a7b6ebc7b280875e29ebbb225896f12317689cdeef7574e0199bd@192.168.1.175:30304"]
```
说明：

- a0913c4ad51613ca15bef3133817dfe4acd0da2094e3e2925108926d2061b643e31069eec81522c8bfc991655a933a1afdd0d11a6dc30b0661287d33d1350227：节点公钥，注意，这里不包含“0x”；
- 192.168.1.171:30304：p2p配置项中，p2p-host和p2p-port的内容；
- static-nodes.json：静态节点需要配置static-nodes.json。该文件要存放在data目录下，即配置文件中data-path配置的目录下，该文件名为默认文件名。如为使用该名称，请使用static-nodes-file参数进行配置。



```bash
#复制static-nodes.json至其他三个节点
cp data/static-nodes.json node2/data/
cp data/static-nodes.json node3/data/
cp data/static-nodes.json node4/data/
```


### 3、创建启动脚本
在BESU_HOME目录下创建启动脚本start.sh。
```bash
#!/bin/bash

BESU_HOME=/opt/besu/besu-clique-network
TESS_HOME=/opt/besu/besu-clique-network/tessera
export JAVA_HOME=/usr/local/jdk-11

which jps || echo "jps未安装，请安装java-1.8.0-openjdk-devel;如使用二进制安装java，请检查该安装包是否已包含jps命令"

start_Tesse() {
[ ! -d logs ] && mkdir logs
#tessera-app-21.4.1-app.jar需要先下载，并复制到$BESU_HOME/lib目录下
nohup /usr/local/jdk-11/bin/java -jar $BESU_HOME/lib/tessera-app-21.4.1-app.jar --configfile tessera/tessera.conf >>$BESU_HOME/logs/tessera.log 2>&1 &
}

stop_tesse() {
PID=$(jps -lmv | grep $BESU_HOME/lib/tessera-app-21.4.1-app.jar | awk '{print $1}')
[ ! $PID ] && echo "PID不存在，请检查服务是否已经启动" && exit 1
kill $PID
[ $? -eq 0 ] && echo "节点已停止"
}

start() {

start_Tesse
nohup $BESU_HOME/bin/besu --config-file=$BESU_HOME/config/config.toml >>$BESU_HOME/logs/besu.log 2>&1 &
echo "查看$BESU_HOME/logs/besu.log确认启动状态"

}

stop() {
PID=$(jps -lmv  | grep org.hyperledger.besu.Besu | grep $BESU_HOME | awk '{print $1}')
[ ! $PID ] && echo "PID不存在，请检查服务是否已经启动" && exit 1
kill $PID
[ $? -eq 0 ] && echo "节点已停止"
}

restart() {
stop
start
}

$1
```


复制启动脚本至节点目录下：
```bash
cp start.sh node2/
cp start.sh node3/
cp start.sh node4/
```
### 4、复制配置目录至其它节点
```bash
#复制node2至192.168.1.173
scp -r /opt/besu/besu-clique-network/node2/* 192.168.1.173:/opt/besu/besu-clique-network
#复制node3至192.168.1.174
scp -r /opt/besu/besu-clique-network/node3/* 192.168.1.174:/opt/besu/besu-clique-network
#复制node4至192.168.1.175
scp -r /opt/besu/besu-clique-network/node4/* 192.168.1.175:/opt/besu/besu-clique-network
```
### 5、各节点使用启动脚本启动服务
```bash
cd /opt/besu/besu-clique-network/
sh start.sh start
```
启动后，查看logs目录下的tessera.log的日志信息以及besu.logs的日志信息，确认启动是否完成。
使用以下命令确认集群存在三个签名者：
`curl -X POST --data '{"jsonrpc":"2.0","method":"clique_getSigners","params":["latest"], "id":1}' 192.168.1.171:18549
`
![image.png](https://cdn.nlark.com/yuque/0/2021/png/2560185/1623839247122-c4cb07f2-8cc0-47f7-9671-1d3747c5b06e.png#align=left&display=inline&height=45&margin=%5Bobject%20Object%5D&name=image.png&originHeight=89&originWidth=1483&size=11934&status=done&style=none&width=741.5)
















