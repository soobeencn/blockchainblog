## 网络背景
明天的网络, 今天需要IPFS: IPFS旨在超越HTTP, 以便为我们所有人构建更好的网络

- 当今的网络效率低下且价格昂贵
HTTP 一次从一台服务器下载文件——但点对点 IPFS 一次从多个节点检索文件，从而显着节省带宽。IPFS 可以节省高达 60% 的视频费用， 从而可以有效地分发大量数据而无需重复。

- 今天的网络无法保存人类的历史
网页的平均寿命是 100 天 ，然后才会永远消失。我们这个时代的媒介不应该如此脆弱。IPFS 使得为镜像数据设置弹性网络变得简单，并且由于**内容寻址**，使用 IPFS 存储的文件会自动进行版本控制。

- 今天的网络是集中的，限制了机会
互联网成为人类历史上最伟大的均衡器之一，推动了创新——但日益巩固的控制威胁着这一进步。IPFS 通过提供使该愿景成为现实的技术，忠于开放、扁平网络的原始愿景。

- 今天的网络沉迷于骨干
IPFS 为创建具有持续可用性的多样化弹性网络提供支持——无论是否有互联网骨干连接。这意味着为发展中国家提供更好的连接性，在自然灾害期间，或者当您使用不稳定的咖啡店 Wi-Fi 时。



## 工作原理

将文件添加到 IPFS 时会发生以下情况——无论将该文件存储在自己的本地节点上，还是由固定服务或支持 IPFS 的应用程序操作的节点上。

1. 当文件添加到 IPFS 时，会被拆分成更小的块，进行加密散列，并赋予一个称为 **内容标识符** (CID)的 **唯一指纹**。此 CID 充当文件的永久记录，因为它在该时间点存在。
2. 当其他节点查找该文件时，它们会询问他们的对等节点谁存储了文件的 CID 引用的内容。当他们查看或下载文件时，他们会缓存一个副本——并成为该内容的另一个提供者，直到他们的缓存被清除。
3. 节点可以 **固定内容** 以永久保留（并提供）它，或者丢弃一段时间未使用的内容以节省空间。这意味着网络中的每个节点 只存储它感兴趣的内容，以及一些有助于确定哪个节点存储什么的索引信息。
4. 如果将文件的新版本添加到 IPFS，它的加密哈希是不同的，因此它会获得一个新的 CID。这意味着 存储在 IPFS 上的文件可以**抵抗篡改和审查** ——对文件的任何更改都不会覆盖原始文件，并且可以重复使用文件中的公共块，以最大限度地降低存储成本。
5. 但是，这并不意味着必须记住一长串 CID — IPFS 可以使用 **IPNS** 分散命名系统找到文件的最新版本，并且 DNSLink 可用于将 CID 映射到 人类可读的 DNS 名称。

## 核心概念
### 内容寻址
_内容寻址是一种用于在信息系统中组织和定位数据的技术，其中用于定位内容的密钥来自内容本身_
#### 基本问题
想象一个key-value存储:
```java
public interface KVStore {
    void put(String key, String value);

    String get(String key);
}
```
这个基本接口对于键值存储非常常见。使用put，我们可以将任何Value与 a相关联Key，稍后当我们需要它时，我们可以查找密钥get并希望得到我们的值。

当我们开始使用这样的界面时，最重要的决定之一就是为键使用什么。如果我们正在构建一个控制访问模式的应用程序，可以使用我们喜欢的任何键并在代码中跟踪它们，或者提出一些规则来映射哪些键应该用于哪种数据.

当许多不协调的各方都同时写信给商店时，事情变得更加复杂。使用一个全局密钥空间，要么每个人都需要就相同的规则达成一致，要么空间需要分成许多“域”或“命名空间”。

假设我们有一个由数千甚至数百万人共享的 **大型键值存储**，每个人在键空间中都有自己的域。这主要解决了写作问题——每个人都可以管理他们的密钥，而无需与其他人协调。

但是，现在我们不太清楚当我们想要get再次查找数据时在哪里查找数据。由于每个域都遵循自己的规则，因此很难知道使用什么密钥来检索内容。此外，如果不同域之间没有协调，最终可能会在 **不同域中多次存储相同的值**，并且没有简单的方法来判断许多键都指向相同的值。

如果这听起来很熟悉，请考虑在解析domain/concepts/content-addressing. 首先，我们的操作系统将查询一个全局共享键值存储，该存储分为许多域：域名系统 (DNS)。DNS 将返回一个 IP 地址，网卡可以使用该地址通过网络发送 HTTP 请求，该站点的命名约定将密钥/concepts/content-addressing转换为响应负载。

网络基本上是“互联网规模”的定义，所以显然这个系统运行得很好。所以有什么问题？

真正的问题是时间。

地址的两个组成部分domain/concepts/content-addressing都是可变的，这意味着它们可以随着时间而改变。如果我们忘记支付账单，域名可能会过期并被出价最高的人购买。或者，如果我们决定对我们的网站结构进行快速而松散的操作而忘记添加重定向，则路径/concepts/content-addressing可能会返回 404 错误。

在网络环境中，一切都是可变的和动态的，这就是它一直以来的方式。**网络从未承诺任何形式的永久性**，无论是在内容还是在内容之间的链接的“元结构”中。结果，**链接丢失** 只是我们都学会忍受的东西。

题外话: (我们通过NFT来思考电子票据的场景)
- 但是对于一个实际上是永久的数字工件，比如 NFT，链接失效是一个存在的问题。链接失效会影响 NFT，因为大多数 NFT 实际上只是链接。大多数区块链网络上的数据存储比传统的在线存储系统要贵得多。例如，根据天然气价格，2021 年 5 月，直接在以太坊上存储 1 兆字节数据的成本约为 21.5 以太币 (ETH) - 当时，这大约是 56,000 美元。

- 为了使代表数字艺术的 NFT 在财务上实用，艺术品本身需要存储在“链下”存储成本可控的地方，同时尽可能少地限制“链上”存储。最简单的方法是仅将链下数据的链接存储在 NFT 本身内部。然而，区块链的永久性只适用于链上数据。如果您存储的 NFT 带有稍后会失效的链接，即使区块链记录保持不变，NFT 的价值也会受到损害。

#### 更强的链接
为了从 NFT 安全地链接到图像和元数据等链下资产，我们需要能够经受住时间冲击的链接。理想的链接将始终解析为永久区块链记录中最初引用的同一内容，并且不会与单个服务器所有者或“域”绑定。

内容寻址为我们提供了我们需要的那种链接。内容寻址系统就像我们的键值存储一样工作，但有一个显着的区别：您不再可以选择键。相反，密钥直接从使用确定性函数存储的值中派生，该确定性函数将始终为相同的内容生成相同的密钥。

现在我们之前的界面看起来像这样：
```java
public interface KVStore {
    String put(String value);
    
    String get(String key);
}
```

我们的方法不接受键和值，put而是只接受一个值并将键返回给调用者。作为不能选择自己的钥匙的交换，你会得到一些有价值的财产。

首先，我们不再需要通过将键空间拆分为域来协调存储的多个写入者。现在有一个通用域——所有可能值的域。如果多个人添加相同的值，则密钥空间中不会发生冲突。他们每个人都从put方法中得到相同的密钥。

这种变化也赋予了我们的 **价值观位置独立性**。在具有多个域的原始键值存储中，我们必须在键中包含域以防止名称冲突。要检索一个值，需要知道它属于哪个域，以及该域的密钥空间中的特定位置。如果我们将 **基于位置的密钥存储在区块链上**，我们检索数据的能力取决于嵌入到我们密钥中的一个域。即使相同的内容存储在其他一千个域中，如果我们依赖的域消失或更改其命名约定，我们的查找也会失败。

#### 如何使用内容寻址
到目前为止，一直在抽象地讨论内容寻址，我们如何才能真正利用内容寻址来制作具有持久链接的 NFT？

最简单的方法是使用IPFS ，星际文件系统。当数据存储在 IPFS 上时，用户可以从任何具有副本的 IPFS 节点获取它，这可以提高数据传输效率并减少任何单个服务器上的负载。当每个用户获取一条数据时，他们会保留一份本地副本，以帮助其他可能稍后请求它的用户。

### 内容持久化

从广义上讲，今天的网络浏览通过所谓的位置寻址进行操作。位置寻址从网络上的特定位置检索在线信息——即从 URL 后面。然而，这有其明显的缺点。位置寻址是集中式的，这意味着控制该位置的人控制内容。位置寻址 URL 后面的任何内容都可以更改：文章的语言、数字艺术品中使用的颜色或记录身份的属性。简而言之，定位地址的 URL 是 **可利用的**。

这个问题的解决方案从内容寻址开始。基于内容的寻址允许您基于唯一指纹（通常称为哈希）访问数据；无论存储在哪里，如果拥有数据的唯一指纹，都能够检索内容。这意味着如果使用内容寻址存储，内容不再从网络上的单个位置检索。相反，当有人请求该内容时，它会从 IPFS 网络上任何拥有它的副本的参与节点中检索。内容片段可以位于许多节点上，并且可以完全从一个节点检索内容，也可以从多个节点以 **零碎的形式组装内容**。

然而，内容寻址只是解决方案的一部分。仅仅因为无论数据存储在哪里都可以通过 IPFS 检索数据，这并不意味着数据可以保证永远存在。要拥有完整的解决方案，**内容持久性** 是关键。

#### 为什么要内容持久化
一旦我们围绕基于内容的寻址重新定位在线内容的检索，问题就变成了：**我们如何确保内容始终可用**？换句话说，我们如何确保内容持续存在？如果没有随着时间的推移可靠存储的内容，即使是内容寻址的网络也会遭受与今天类似的危险；除非内容持续存在，否则我们将面临碎片化、不完整和健忘的网络的风险。

与数据和信息空间中的大多数解决方案一样，存在解决内容持久性的集中式和分散式选项。集中式选项是使用承诺始终将内容存储在其服务器上的服务。然而，**集中式存储并没有实现真正的持久性**，因为它受到单个集中式故障点的影响。

另一种选择，**分散内容持久性**，是确保内容随时间保持可用的唯一方法。使用完全独立的、可互操作的节点来存储由强大的加密保证支持的数据，可以防止信息由于任何集中式服务的动作（或不作为！）而变得不可用。如何才能做到这一点？
即: ipfs-cluster + ipfs + swarmkey

## 产品架构
IPFS 是一个点对点 (p2p) 存储网络。内容可以通过位于世界任何地方的同行访问，这些同行可能会转发信息、存储信息或两者兼而有之。IPFS知道如何使用其内容地址而不是位置来查找你要求的内容。

了解IPFS有三项基本原则：

- 通过内容寻址进行唯一识别
- 通过有向无环图（DAG）链接的内容
- 通过分布式散列表（DHT）发现内容

这三个原则相互建立，以实现IPFS生态系统。

![](http://mweb-storage.oss-cn-shenzhen.aliyuncs.com/2022/03/31/16474261020878.jpg?image/auto-orient,1/quality,Q_90)


## 生产部署
**安全的共享、复制和存储数据**

![](http://mweb-storage.oss-cn-shenzhen.aliyuncs.com/2022/03/31/16469688305446.jpg?image/auto-orient,1/quality,Q_90)
关键: 
- 部署完整的集群环境, 至少3个节点起, 这点取决于raft算法
- 每个ipfs-cluster需要依托于ipfs守护进程建立, 所以部署生产级集群环境, 每个节点必须额外部署ipfs守护进程
### IPFS-必读
IFPS 网络有两种类型：公有和私有。公有 IPFS 网络上的所有文件对每个人来说都是可访问的。

因为大多数商业应用，尤其是企业级的应用，需要对他们的数据拥有完全的控制权，网络可以被公众访问是不可接受的。
IPFS 隐私功能可以帮助关闭对应特定实体的网络。

#### 重点
- **IPFS**：一种协议和网络，用于在分布式文件系统中创建可寻址的点对点内容存储和共享超媒体的方法

- **Private IPFS**：允许 IPFS 只连接到拥有**共享密钥**的其他对等点。对于IPFS私有网络，每个节点指定它将连接到哪些其他节点。网络中的节点不响应来自网络外部节点的通信。

- **IPFS-Cluster**：IPFS-cluster 是一个独立的应用和命令行客户端，它在一组 IPFS 守护进程之间分配、复制和跟踪 pins。IPFS-Cluster 使用基于 leader 的共识算法 Raft 来协调 pinset 的存储，将数据集分布到参与节点。

值得注意的是，私有网络是在核心 IPFS 功能中实现的默认特性，IPFS-Cluster 是其单独的应用程序。IPFS 和 IPFS-Cluster 应用程序以不同的包安装，以各自的进程运行，它们具有不同的节点 IP 以及 API 入口和端口。
IPFS-Cluster 守护进程依赖于 IPFS 守护进程，应该在 IPFS 之后启动。

### 启动私有网络
#### 必要条件
- go env: https://go.dev/doc/install


#### step1: 安装ipfs
- ipfs: https://github.com/ipfs/go-ipfs/releases

#### step2: 下载swarm.key
_https://github.com/Kubuxu/go-ipfs-swarm-key-gen/

- 下载源文件
- 生成密钥: 
    ```bash
    ./ipfs-swarm-key-gen &> ~/.ipfs/swarm.key
    ```
- 拷贝swarm.key 至其他私有网络的节点目录下   
    ```bash
    ./ipfs/
    ```
#### step3: 启动第一个节点
```vb
ipfs daemon
```

查看p2p地址并记录:
```bash
ipfs id
```
记录Address数组中第二个:

`/ip4/172.25.255.188/tcp/4001/p2p/12D3KooWSvvtpUfvZbATqMVDbcJLa5x1HCxnZPSzzQ16UKiHn5pF`
    
    
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
```bash 
echo hi IPFS &> file.txt
```
```bash
ipfs add file.txt
```


返回:
```bash
added Qme65DATcv3WFJqjhYn7tXKWz1QFYBFN9Bb4yDxbzETuFM file.txt
```
注意保存added后面的字符串

在本机节点以及其余节点查看
```bash
ipfs cat Qme65DATcv3WFJqjhYn7tXKWz1QFYBFN9Bb4yDxbzETuFM
```

如果在其余节点均能够查看到file.txt中的信息, 说明私有网络搭建成功.

---------------
### 启动IPFS-守护进程
为了让 IPFS 守护进程可以在我们退出控制台会话后继续运行，我们将会创建 systemd 服务。在我们做这个之前，先停止你的 ipfs 守护进程。为新服务创建一个文件。
```bash
sudo vim /etc/systemd/system/ipfs.service
```

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
```bash
sudo systemctl daemon-reload
```
```bash
sudo systemctl enable ipfs
```
```bash
sudo systemctl start ipfs
```
```bash
sudo systemctl status ipfs
```
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
    ```bash
    curl -L -O https://dist.ipfs.io/ipfs-cluster-service/v0.14.5/ipfs-cluster-service_v0.14.5_linux-amd64.tar.gz
    ```

- ipfs-cluster-ctl
    ```bash
    curl -L -O https://dist.ipfs.io/ipfs-cluster-ctl/v0.14.5/ipfs-cluster-ctl_v0.14.5_linux-amd64.tar.gz
    ```

- ipfs-cluster-follow
    ```bash
    curl -L -O https://dist.ipfs.io/ipfs-cluster-follow/v0.14.5/ipfs-cluster-follow_v0.14.5_linux-amd64.tar.gz
    ```

**备注**: 如果遇到无法下载的情况, 可先科学上网, 下载至本地, 再push到remote server.

- 解压三个文件夹, 初始化IPFS-Cluster
    ```bash
    cd ipfs-cluster-service/
    ```
    ```bash
    ./ipfs-cluster.service init
    ```
    ```bash
    cd /root/.ipfs-cluster/
    ```
    ```bash
    vim service.json
    ```
- 记录下: secret
    > "secret": "d88fcac50912c5d681ccebc888bbe6a449aa548a30c952f9749fb31fcd2cc25e",

- 查看Node-1的p2p地址并记录:
    ```bash
    ./ipfs-cluster-ctl id
    ```
#### step2: 启动其他节点, 并修改service.json中的secret, 与节点1保持一致

```bash
./ipfs-cluster-service init --custom-secret d88fcac50912c5d681ccebc888bbe6a449aa548a30c952f9749fb31fcd2cc25e
```
查看:
```bash
vim /root/.ipfs-cluster/service.json
```

启动: 以引导的方式启动
```shell
./ipfs-cluster-service daemon --bootstrap /ip4/172.25.255.187/tcp/9096/p2p/12D3KooWR7TbR2VEifk4ZGAZ1h4HnnpA6M3kdnYSUeYyntyEkXt7
```
#### step3: 查看节点状态
```bash
./ipfs-cluster-ctl peers ls
```
### 启动IPFS-Cluster-守护进程
```bash
sudo vim /etc/systemd/system/ipfs-cluster.service
```
cat result:
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
重载:
```bash
sudo systemctl daemon-reload
```
```bash
sudo systemctl enable ipfs-cluster
```
```bash
sudo systemctl start ipfs-cluster
```
```bash
sudo systemctl status ipfs-cluster
```
------------------

## API接入

- RESTAPI (HTTP): /ip4/127.0.0.1/tcp/9094

