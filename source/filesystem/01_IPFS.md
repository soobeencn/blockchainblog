# IPFS-星际文件系统
## 介绍
IPFS是一个用于存储和访问文件、网站、应用程序和数据的分布式系统。
## 基础架构

![](http://mweb-storage.oss-cn-shenzhen.aliyuncs.com/2022/03/14/16469688305446.jpg?image/auto-orient,1/quality,Q_90)
关键: 
- 部署完整的集群环境, 至少3个节点起, 这点取决于raft算法
- 每个ipfs-cluster需要依托于ipfs守护进程建立, 所以部署生产级集群环境, 每个节点必须额外部署ipfs守护进程

## 工作原理
IPFS 是一个点对点 (p2p) 存储网络。内容可以通过位于世界任何地方的同行访问，这些同行可能会转发信息、存储信息或两者兼而有之。IPFS知道如何使用其内容地址而不是位置来查找你要求的内容。

了解IPFS有三项基本原则：

- 通过内容寻址进行唯一识别
- 通过有向无环图（DAG）链接的内容
- 通过分布式散列表（DHT）发现内容

这三个原则相互建立，以实现IPFS生态系统。让我们从内容寻址和内容的唯一标识开始。

## 产品架构
## 生产部署
**安全的共享、复制和存储数据**
### 私有网络概述
IFPS 网络有两种类型：公有和私有。公有 IPFS 网络上的所有文件对每个人来说都是可访问的。
因为大多数商业应用，尤其是企业级的应用，需要对他们的数据拥有完全的控制权，网络可以被公众访问是不可接受的。
IPFS 隐私功能可以帮助关闭对应特定实体的网络。

#### 名词解释
- **IPFS**：一种协议和网络，用于在分布式文件系统中创建可寻址的点对点内容存储和共享超媒体的方法

- **Private IPFS**：允许 IPFS 只连接到拥有共享密钥的其他对等点。对于IPFS私有网络，每个节点指定它将连接到哪些其他节点。网络中的节点不响应来自网络外部节点的通信。

- **IPFS-Cluster**：IPFS-cluster 是一个独立的应用和命令行客户端，它在一组 IPFS 守护进程之间分配、复制和跟踪 pins。IPFS-Cluster 使用基于 leader 的共识算法 Raft 来协调 pinset 的存储，将数据集分布到参与节点。

值得注意的是，私有网络是在核心 IPFS 功能中实现的默认特性，IPFS-Cluster 是其单独的应用程序。IPFS 和 IPFS-Cluster 应用程序以不同的包安装，以各自的进程运行，它们具有不同的节点 IP 以及 API 入口和端口。IPFS-Cluster 守护进程依赖于 IPFS 守护进程，应该在 IPFS 之后启动。


### 启动私有网络
#### 必要条件
- go env: https://go.dev/doc/install

#### step1: 安装ipfs
- ipfs: https://github.com/ipfs/go-ipfs/releases

#### step2: 下载swarm.key
https://github.com/Kubuxu/go-ipfs-swarm-key-gen/

- 下载源文件
- 生成密钥: 
    > ./ipfs-swarm-key-gen &> ~/.ipfs/swarm.key

- 拷贝swarm.key 至其他私有网络的节点目录下   
    > ./ipfs/

#### step3: 启动第一个节点
> ipfs daemon

查看p2p地址并记录:
> ipfs id

```bash
{
    "ID": "12D3KooWSvvtpUfvZbATqMVDbcJLa5x1HCxnZPSzzQ16UKiHn5pF",
    "PublicKey": "CAESIP5FKlVMj23QrF4GDnhpJm2t5I6IndqdEEIiibP44cTs",
    "Addresses": [
            "/ip4/127.0.0.1/tcp/4001/p2p/12D3KooWSvvtpUfvZbATqMVDbcJLa5x1HCxnZPSzzQ16UKiHn5pF",
            "/ip4/172.25.255.188/tcp/4001/p2p/12D3KooWSvvtpUfvZbATqMVDbcJLa5x1HCxnZPSzzQ16UKiHn5pF",
            "/ip6/::1/tcp/4001/p2p/12D3KooWSvvtpUfvZbATqMVDbcJLa5x1HCxnZPSzzQ16UKiHn5pF"
    ],
    "AgentVersion": "go-ipfs/0.12.0/",
    "ProtocolVersion": "ipfs/0.1.0",
    "Protocols": [
            "/floodsub/1.0.0",
            "/ipfs/bitswap",
            "/ipfs/bitswap/1.0.0",
            "/ipfs/bitswap/1.1.0",
            "/ipfs/bitswap/1.2.0",
            "/ipfs/id/1.0.0",
            "/ipfs/id/push/1.0.0",
            "/ipfs/lan/kad/1.0.0",
            "/ipfs/ping/1.0.0",
            "/libp2p/autonat/1.0.0",
            "/libp2p/circuit/relay/0.1.0",
            "/libp2p/circuit/relay/0.2.0/stop",
            "/libp2p/fetch/0.0.1",
            "/meshsub/1.0.0",
            "/meshsub/1.1.0",
            "/p2p/id/delta/1.0.0",
            "/x/"
    ]
}
```
记录Address数组中第二个:
> /ip4/172.25.255.188/tcp/4001/p2p/12D3KooWSvvtpUfvZbATqMVDbcJLa5x1HCxnZPSzzQ16UKiHn5pF
    
    
#### step4: 删除默认p2p节点

> ipfs bootstrap
    
> ipfs bootstrap rm all

#### step5: 其他节点添加引导节点p2p地址
> ipfs bootstrap add  /ip4/172.25.255.188/tcp/4001/p2p/12D3KooWSvvtpUfvZbATqMVDbcJLa5x1HCxnZPSzzQ16UKiHn5pF

#### step6: 设置环境变量

设置环境变量 LIBP2P_FORCE_PNET 来强制网络使用私有模式：
> export LIBP2P_FORCE_PNET=1

#### step7: 配置ip通信(如需外网访问)
修改API、Gateway
> vim /root/.ipfs/config

#### step8: 启动节点并测试
至此,已经完成了所有配置,让我们启动所有节点并测试
> ipfs daemon

在其中一个节点中添加文件, 从另外的节点查看
> echo hi IPFS &> file.txt

> ipfs add file.txt

返回:
```bash
added Qme65DATcv3WFJqjhYn7tXKWz1QFYBFN9Bb4yDxbzETuFM file.txt
```
注意保存added后面的字符串

在本机节点以及其余节点查看
> ipfs cat Qme65DATcv3WFJqjhYn7tXKWz1QFYBFN9Bb4yDxbzETuFM

如果在其余节点均能够查看到file.txt中的信息, 说明私有网络搭建成功.

---------------
### 启动IPFS-守护进程
为了让 IPFS 守护进程可以在我们退出控制台会话后继续运行，我们将会创建 systemd 服务。在我们做这个之前，先停止你的 ipfs 守护进程。为新服务创建一个文件。
> sudo vim /etc/systemd/system/ipfs.service

然后添加如下配置：
```bash
[Unit]
 Description=IPFS Daemon
 After=syslog.target network.target remote-fs.target nss-lookup.target
 [Service]
 Type=simple
 ExecStart=/usr/local/bin/ipfs daemon --enable-namesys-pubsub
 User=root
 [Install]
 WantedBy=multi-user.target
```
添加新服务:
> sudo systemctl daemon-reload

> sudo systemctl enable ipfs

> sudo systemctl start ipfs

> sudo systemctl status ipfs

重新启动系统，检查IPFS守护进程是否处于活动状态并正在运行，然后可以再次尝试从一个节点添加文件并从另一个节点访问它。

我们已经完成了创建私有 IPFS 网络并将其守护进程作为服务运行的工作。在这个阶段，你应该已经有了三个私有网络结点。现在，让我们创建用于数据复制的 IPFS-CLUSTER。

--------------------------

### 启动IPFS-Cluster
#### 必要条件
- ipfs 守护进程



在创建私有 IPFS 网络之后，我们可以开始在 IPFS 之上部署 IPFS-Cluster，以实现数据的自动复制和更好的数据管理。

有两种方法来组织 IPFS 集群:
- 第一种是设置一个固定的 peerset(所以在创建集群后，你不能增加更多的结点)
- 另一个是通过**引导节点**(可以在集群创建后添加新的结点-我们使用这种方式创建更加灵活)。

#### step1: 初始化节点
IPFS 集群在IPFS 分布页面上为多个平台提供了预构建的**二进制**文件：
- ipfs-cluster-service
    > curl -L -O https://dist.ipfs.io/ipfs-cluster-service/v0.14.5/ipfs-cluster-service_v0.14.5_linux-amd64.tar.gz

- ipfs-cluster-ctl
    > curl -L -O https://dist.ipfs.io/ipfs-cluster-ctl/v0.14.5/ipfs-cluster-ctl_v0.14.5_linux-amd64.tar.gz

- ipfs-cluster-follow
    > curl -L -O https://dist.ipfs.io/ipfs-cluster-follow/v0.14.5/ipfs-cluster-follow_v0.14.5_linux-amd64.tar.gz

**备注**: 如果遇到无法下载的情况, 可先科学上网, 下载至本地, 再push到remote server.

- 解压三个文件夹, 初始化IPFS-Cluster
    > cd ipfs-cluster-service/

    > ./ipfs-cluster.service init

    > cd /root/.ipfs-cluster/

    > vim service.json

- 记录下: secret
    > "secret": "d88fcac50912c5d681ccebc888bbe6a449aa548a30c952f9749fb31fcd2cc25e",

- 查看Node-1的p2p地址并记录:
    > ./ipfs-cluster-ctl id

#### step2: 启动其他节点, 并修改service.json中的secret, 与节点1保持一致

> ./ipfs-cluster-service init --custom-secret d88fcac50912c5d681ccebc888bbe6a449aa548a30c952f9749fb31fcd2cc25e

> vim /root/.ipfs-cluster/service.json

启动: 以引导的方式启动
> ./ipfs-cluster-service daemon --bootstrap /ip4/172.25.255.187/tcp/9096/p2p/12D3KooWR7TbR2VEifk4ZGAZ1h4HnnpA6M3kdnYSUeYyntyEkXt7

#### step3: 查看节点状态
> ./ipfs-cluster-ctl peers ls
```bash
12D3KooWNZejNHbnGcVL2p4oZa7Paf7XWAXHD5kc3qx523NJY15t | aliyun-im | Sees 2 other peers
  > Addresses:
    - /ip4/127.0.0.1/tcp/9096/p2p/12D3KooWNZejNHbnGcVL2p4oZa7Paf7XWAXHD5kc3qx523NJY15t
    - /ip4/172.25.96.29/tcp/9096/p2p/12D3KooWNZejNHbnGcVL2p4oZa7Paf7XWAXHD5kc3qx523NJY15t
  > IPFS: 12D3KooWJ711y8M3jJD7o8FxrEqiFmP6vYZgfs6nnejN6U7cDqRy
    - /ip4/120.24.57.63/tcp/4001/p2p/12D3KooWJ711y8M3jJD7o8FxrEqiFmP6vYZgfs6nnejN6U7cDqRy
    - /ip4/120.24.57.63/udp/4001/quic/p2p/12D3KooWJ711y8M3jJD7o8FxrEqiFmP6vYZgfs6nnejN6U7cDqRy
    - /ip4/127.0.0.1/tcp/4001/p2p/12D3KooWJ711y8M3jJD7o8FxrEqiFmP6vYZgfs6nnejN6U7cDqRy
    - /ip4/127.0.0.1/udp/4001/quic/p2p/12D3KooWJ711y8M3jJD7o8FxrEqiFmP6vYZgfs6nnejN6U7cDqRy
    - /ip4/172.25.96.29/tcp/4001/p2p/12D3KooWJ711y8M3jJD7o8FxrEqiFmP6vYZgfs6nnejN6U7cDqRy
    - /ip4/172.25.96.29/udp/4001/quic/p2p/12D3KooWJ711y8M3jJD7o8FxrEqiFmP6vYZgfs6nnejN6U7cDqRy
    - /ip6/::1/tcp/4001/p2p/12D3KooWJ711y8M3jJD7o8FxrEqiFmP6vYZgfs6nnejN6U7cDqRy
    - /ip6/::1/udp/4001/quic/p2p/12D3KooWJ711y8M3jJD7o8FxrEqiFmP6vYZgfs6nnejN6U7cDqRy
12D3KooWR7TbR2VEifk4ZGAZ1h4HnnpA6M3kdnYSUeYyntyEkXt7 | aliyun-box | Sees 2 other peers
  > Addresses:
    - /ip4/127.0.0.1/tcp/9096/p2p/12D3KooWR7TbR2VEifk4ZGAZ1h4HnnpA6M3kdnYSUeYyntyEkXt7
    - /ip4/172.25.255.187/tcp/9096/p2p/12D3KooWR7TbR2VEifk4ZGAZ1h4HnnpA6M3kdnYSUeYyntyEkXt7
  > IPFS: 12D3KooWB4Pi8w6xRmHpgX748edEbRBAfrtC7ykDeZTS3ET1MmrG
    - /ip4/120.24.204.202/tcp/4001/p2p/12D3KooWB4Pi8w6xRmHpgX748edEbRBAfrtC7ykDeZTS3ET1MmrG
    - /ip4/120.24.204.202/udp/4001/quic/p2p/12D3KooWB4Pi8w6xRmHpgX748edEbRBAfrtC7ykDeZTS3ET1MmrG
    - /ip4/127.0.0.1/tcp/4001/p2p/12D3KooWB4Pi8w6xRmHpgX748edEbRBAfrtC7ykDeZTS3ET1MmrG
    - /ip4/127.0.0.1/udp/4001/quic/p2p/12D3KooWB4Pi8w6xRmHpgX748edEbRBAfrtC7ykDeZTS3ET1MmrG
    - /ip4/172.25.255.187/tcp/4001/p2p/12D3KooWB4Pi8w6xRmHpgX748edEbRBAfrtC7ykDeZTS3ET1MmrG
    - /ip4/172.25.255.187/udp/4001/quic/p2p/12D3KooWB4Pi8w6xRmHpgX748edEbRBAfrtC7ykDeZTS3ET1MmrG
    - /ip6/::1/tcp/4001/p2p/12D3KooWB4Pi8w6xRmHpgX748edEbRBAfrtC7ykDeZTS3ET1MmrG
    - /ip6/::1/udp/4001/quic/p2p/12D3KooWB4Pi8w6xRmHpgX748edEbRBAfrtC7ykDeZTS3ET1MmrG
12D3KooWRNwgECVFi7hrEaRVPjbR4sVeT6kSn2VdJDzXSA2V5owX | aliyun-common | Sees 2 other peers
  > Addresses:
    - /ip4/127.0.0.1/tcp/9096/p2p/12D3KooWRNwgECVFi7hrEaRVPjbR4sVeT6kSn2VdJDzXSA2V5owX
    - /ip4/172.25.255.188/tcp/9096/p2p/12D3KooWRNwgECVFi7hrEaRVPjbR4sVeT6kSn2VdJDzXSA2V5owX
  > IPFS: 12D3KooWSvvtpUfvZbATqMVDbcJLa5x1HCxnZPSzzQ16UKiHn5pF
    - /ip4/112.74.169.88/tcp/4001/p2p/12D3KooWSvvtpUfvZbATqMVDbcJLa5x1HCxnZPSzzQ16UKiHn5pF
    - /ip4/112.74.169.88/udp/4001/quic/p2p/12D3KooWSvvtpUfvZbATqMVDbcJLa5x1HCxnZPSzzQ16UKiHn5pF
    - /ip4/127.0.0.1/tcp/4001/p2p/12D3KooWSvvtpUfvZbATqMVDbcJLa5x1HCxnZPSzzQ16UKiHn5pF
    - /ip4/127.0.0.1/udp/4001/quic/p2p/12D3KooWSvvtpUfvZbATqMVDbcJLa5x1HCxnZPSzzQ16UKiHn5pF
    - /ip4/172.25.255.188/tcp/4001/p2p/12D3KooWSvvtpUfvZbATqMVDbcJLa5x1HCxnZPSzzQ16UKiHn5pF
    - /ip4/172.25.255.188/udp/4001/quic/p2p/12D3KooWSvvtpUfvZbATqMVDbcJLa5x1HCxnZPSzzQ16UKiHn5pF
    - /ip6/::1/tcp/4001/p2p/12D3KooWSvvtpUfvZbATqMVDbcJLa5x1HCxnZPSzzQ16UKiHn5pF
    - /ip6/::1/udp/4001/quic/p2p/12D3KooWSvvtpUfvZbATqMVDbcJLa5x1HCxnZPSzzQ16UKiHn5pF
```

### 启动IPFS-Cluster-守护进程
> sudo vim /etc/systemd/system/ipfs-cluster.service

```bash
[Unit]
Description=IPFS-Cluster Daemon
Requires=ipfs
After=syslog.target network.target remote-fs.target nss-lookup.target ipfs
[Service]
Type=simple
ExecStart=/root/ipfs-cluster/ipfs-cluster-service/ipfs-cluster-service daemon
User=root
[Install]
WantedBy=multi-user.target
```
> sudo systemctl daemon-reload

> sudo systemctl enable ipfs-cluster

> sudo systemctl start ipfs-cluster

> sudo systemctl status ipfs-cluster

------------------

## API接入

- RESTAPI (HTTP): /ip4/127.0.0.1/tcp/9094
- QmQzqrM3fCdNq3BWckXmR1zWJ4oiQBUWVm7iRFqeGTdGxE





