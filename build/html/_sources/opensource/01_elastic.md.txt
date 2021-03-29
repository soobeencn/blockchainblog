# Elastic Stack

- 100% 开源
- kibana+elasticsearch+logstash/beats

## kibana

Kibana 是通向 Elastic 产品集的窗口， 它可以在 Elasticsearch 中对数据进行视觉探索和实时分析

### Elastic 软件生态

#### 解决方案

- 搜索
- 地理位置
- 日志
- 指标
- 安全
- APM

#### Elastic 大数据平台

- 数据展示： Kibana
- 存储、索引、计算、分析： elasticsearch
- 数据抓取：Logstash、Beats


#### 功能特性

- 机器学习
- 数据关联分析
- 规则告警
- 多集群监控
- 报表
- 高级安全

#### 服务提供

- Elastic Cloud
- Elastic 企业云

### 本地搭建

* 下载压缩包、yum apt、docker
* 配置单节点ES
    - elasticsearch.yml 单节点试验可以不用修改, 后面可以打开安全配置选项
    - 配置缺省安全选项：添加
      > xpack.security.enabled: true
      
    - 配置tcp通道加密：
        > xpack.security.transport.ssl.enabled: true
          xpack.security.transport.ssl.verification_mode: certificate
          xpack.security.transport.ssl.keystore.path: elastic-certificates.p12
          xpack.security.transport.ssl.truststore.path: elastic-certificates.p12
        
    - 生成keystore和truststore
        - 进入elasticsearch/bin目录, 将证书文件输出到config目录下
        
            > bin/elasticsearch-certutil cert -out config/elastic-certificates.p12 -pass ""
    - 手动生成密码
    
        > bin/elasticsearch-setup-passwords interactive
    
* 配置kibana
    - kibana.yml
        > elasticsearch.hosts: ["http://localhost:9200"]

* 开启安全设置
    - 从6.8、7.1开始免费提供基本安全设置
    - elasticsearch
        - elasticsearch.yml 添加：
        
            > xpack.security.enabled: true
    - 设置缺省用户密码
        - bin/elasticsearch-setup-passwords interactive
    - kibana
        - kibana.yml 打开注释，修改默认配置：
        
            > elasticsearch.username: "kibana"
              elasticsearch.password: "elastic123"

        ```angular2html
        超级管理员：
        elastic
        elastic123
        
        apm_system
        elastic123
        
        kibana
        kibana123
    
        ～
        ～123
        ```

* 常用管理插件
    - stack monitoring
    - APM
    - discovery
    
-----------------------------------
## Logstash

Logstash 是 Elastic Stack 的中央数据流引擎， 用于收集、丰富和统一所有数据，而不管格式或模式。 当与Elasticsearch，Kibana，及 Beats 共同使用的时候便会拥有特别强大的实时处理能力。

### Logstash入门

_the dataflow engine_

- 开源的流式 **ETL** 引擎
- 几分钟即可构建 **数据流** 处理管道
- 具有自适应缓冲的 **水平伸缩性** 和 **弹性**
- 数据来源中立性， 支持 **众多数据源**
- 丰富的周边插件， 目前200多个的第三方集成和处理器
- Elastic Stack自带的**监控**和**管理**

### 系统结构

<table>
    <tr>
        <th colspan="3">Logstash</th>
    </tr>
    <tr>
        <th>Inputs</th><th>Filters</th><th>Outputs</th>
    </tr>
    <tr>
        <td rowspan="1">Beats</td>
        <td>Structure<br> Transform <br>Normalize</td>
        <td>ElasticSearch</td>
    </tr>
    <tr>
        <td>
            TCP <br>
            UDP <br>
            HTTP <br>
        </td>
        <td>GeoIP Enrichment <br>
            External Lookups Enrichment<br>
            CIDR & DNS Lookups
        </td>
        <td rowspan="1">TCP <br>
                        UDP <br> 
                        HTTP
        </td>
    </tr>
     <tr>
        <td>
            JDBC <br>
            HTTP Poller<br>
        </td>
        <td>GeoIP Enrichment <br>
            External Lookups Enrichment<br>
            CIDR & DNS Lookups
        </td>
        <td rowspan="1">Other Storage Queues
        </td>
    </tr>
    <tr>
        <th colspan="3">channel <BR>
                        Dead Letter Queues <br>
                        Persistent Queues 
        </th>
    </tr>
</table>

**备注:**

_1.At-least-once: 交付保障和基于持久化队列的自适应缓冲_  
_2.将错误事件发送到死信队列(dead letter queue)中, 用于离线处理和重放_

### 动态管道

|消息处理机制|
|:------:|
|Apache Pipeline|
|JDBC Pipeline|
|Netflow Pipeline|

- 带条件和多管道的直接数据流
- 使用身份认证和加密来确保传输安全
- 使用modules来一键交付
- 可轻松构建自定义插件集成和处理器

## Beats
### 接入指南

- 获取资源
  > curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.10.2-linux-x86_64.tar.gz

- 解压
  > tar zxvf filebeat-7.10.2-linux-x86_64.tar.gz

- 修改配置文件
  > vim filebeat.yml
  
  ```
  setup.kibana:
    host: "mykibanahost:5601" 
    username: "my_kibana_user"  
    password: "{pwd}"
  
  output.elasticsearch:
    hosts: ["localhost:9200"]
    username: "filebeat_internal"
    password: "YOUR_PASSWORD"         
  ```   
- 加载列表信息
  > ./filebeat modules list

- 自定义加载关联组件
  > ./filebeat modules enable mongodb system nginx kafka mysql
  
- 自动配置assets信息
  > ./filebeat setup -e
  
  _-e is optional and sends output to standard error instead of the configured log output._
- 启动
  > ./filebeat -e  
  
  use root run: 
  > ./filebeat -e --strict.perms=false

- 查看日志
  > 如果没有异常信息，打开kibana -> discovery -> filebeat-*

- 自定义日志录入:  打开 filebeat.yml, update input or input stream, such as:

          filebeat.inputs:
          # Each - is an input. Most options can be set at the input level, so
          # you can use different inputs for various configurations.
          # Below are the input specific configurations.
          type: log
          # Change to true to enable this input configuration.        
          enabled: true
          # Paths that should be crawled and fetched. Glob based paths.        
            paths:
              - /var/log/*.log
              # update the path of your tomcat
              - /home/share/local-jar/startup.log

- ***FAQ***  
  关于启动filebeat遇到的一些常见问题：
    - 使用非root用户启动， 应当先授权目标用户的文件 744权限，如果权限过高， 则会启动失败；
    - 使用root用户启动，则按照下方规则进行配置：
    
        > sudo chown root filebeat.yml 
          sudo chown root modules.d/system.yml
          sudo ./filebeat -e
      
### 原理
#### 一个基本配置

```js
input {
    beats { port => 5043 }
}
filter {
    mutate { lowercase => ["message"] }
}
output {
    elasticsearch { }
}
```
#### 一个事件
- Logstash主要的数据单元就是事件
- 他们是文档类型， 和Json文档类型很相似，支持任意层次结构和类型

```js
{
    "@timestamp" => 2020-02-02T01-01-01,
    "message" => "bar",
    "some other field" => {
        "has comflex values" => 123
    }     
}
```

#### 一个管道
- pipelines 可伸缩
- 支持多个input
- 支持多个filter， output
- 一个Logstash，支持多个管道

### 队列和交付保障

|InMemoryQueue|Persistent Queue|
| :----: | :----: |
|非常快|快|
|所有数据都在内存|收到的数据先落地磁盘，直到交付完成|
|没有落地|落地|
|正在处理的数据可能会因为非正常关闭而丢失|持久化，非正常关闭不会丢失数据|

### 至少交付一次（At Least Once Delivery）
- 大部分插件支持
- 大部分情况，消息只交付一次
- 非正常关闭下，持久化队列里面的事件，我们可能会运行worker不止一次，可能会产生重复消息
- 幂等操作（永远写相同的ID）可以确保消息不重复

### packetbeat
- 实时抓取网络包
- 自动解析应用层协议
- 替代wireshark

### DEMO
#### 下载 elasticsearch , beats, logstash, kibana
#### 按照自定义需求进行配置 --> kibana展示

### Logstash Modules
_Immediate Insights with Modules_
- 特定数据类型的一键交付
- 从数据到监控面板只需一步
- 自动解析和充实数据
- 默认的监控面板、告警设置、机器学习任务
- [可用的modules](www.elastic.co/guide/en/logstash/current/logstash-modules.html)
  
### codecs & serialize
***using codecs to serialize/deserialize data***
#### demo：
```js
input {
    file { 
        // Deserialize new line separated JSON
        path => "some/json.log", codec => json_lines
    }
}
output {
    // Serialize to the msgpack format
    redis { 
        codec => msgpack 
    }
}
```
--------------
### 监控和管理

Monitoring
- 使用 Monitoring UI 集中监控和管理多个Logstash部署
- 通过Pipeline Viewer 快速定位和分析瓶颈
- 借助 Monitoring API 直接从Logstash节点获取节点指标信息

Pipeline Management
- 一个UI来管理多个Logstash节点上的多个管道
- Logstash可以轮询和动态重新加载更新的管道
- 使用X-Pack Security 特性来控制管道管理的访问
----------------------------------------------

## Metricbeat 

### 接入指南

- 同filebeat类似，详细内容参考filebeat接入指南

----------------------

## APM [application performance monitoring]


对于大部分应用程序来说性能都是很重要的一个因素，尤其对于比如网站、手机app等直接由用户访问的应用来说更是如此，因为性能较差的应用将会直接影响其用户体验。因此，能对应用进行性能监控变得非常重要，这将帮助我们找到性能瓶颈并优化。

应用性能监控（Application Performance Monitoring，APM），是对应用程序性能和可用性的监视和管理。 APM努力检测和诊断复杂的应用程序性能问题，以维持预期的服务等级。

### APM Server
APM 服务器会从 APM 代理处接收数据，然后再将这些数据转换为 Elasticsearch 文档。
它是通过暴露 HTTP 服务器端点（代理会将所收集的 APM 数据流式传输到此端点）来实现这一点的。APM 服务器对来自 APM 代理的事件进行验证和处理之后，服务器会将数据转换为 Elasticsearch 文档，并将这些文档存储在相应的 Elasticsearch 索引中。

### APM 代理
APM 代理是以您服务所用的相同语言编写的开源库。您可以像安装任何其他库一样，将 APM 代理安装到您的服务中。它们可以检测您的代码并在运行时收集性能数据和错误。这些数据会缓存一小段时间，然后发送到 APM 服务器。

### APM 应用
查找并修复代码中存在的问题归根结底就是搜索。通过 Kibana 中的专用 APM 应用，您能够识别瓶颈并在代码层面准确定位到存在问题的地方。因此，您能够编写更好、更高效的代码，进而帮助您加快“开发-测试-部署”周期，让您的应用程序运行更快，客户体验更佳。

### 分布式跟踪
纳闷您的请求是如何流经整个基础架构的？通过分布式跟踪将所有内容整合到一起，清晰查看您的各项服务之间的交互情况。查找路径中哪个位置发生了延时问题，然后准确定位到需加以优化的组件。

效果：
- 对JVM的监控：
- 同时支持配置alerting阈值

### 运行apm插件

    java -javaagent:/home/share/plugins/elastic-apm-agent-1.21.0.jar -Delastic.apm.service_name=my-cool-service -Delastic.apm.application_packages=org.example,org.another.example -Delastic.apm.server_url=http://localhost:8200 -jar hyperchain-0.0.1-SNAPSHOT.jar

------------------------------------------------------------------

**X-Pack plugin 需要商业授权**

- Security
- Alerting
- Monitoring
- Reporting
- Graph
- Machine Language

--------------------------

## ElasticSearch

### 对比mysql
|ElasticSearch|Mysql|
|:---:|:---:|
|Index|Database|
|Type|Table|
|Document|Row|
|Field|Column|
|Mapping|Schema|
|Query DSL|SQL|
|GET http://...|select * from table ...|
|PUT http://...|update table set ...|

**备注：**
- ElasticSearch 中的索引 Index 类似于 MySQL 中的数据库 Database；
1. - ElasticSearch 中的类型 Type 类似于 MySQL 中的表 Table；需要注意，这个概念在 7.x 版本中被完全删除，而且概念上和 Table 也有较大差异；
- ElasticSearch 中的文档 Document 类似于 MySQL 中的数据行 Row，每个文档由多个字段 Filed 组成，这个Filed 就类似于 MySQL 的 Column；
- ElasticSearch 中的映射 Mapping 是对索引库中的索引字段及其数据类型进行定义，类似于关系型数据库中的表结构 Schema；
- ElasticSearch 使用自己的领域语言 Query DSL 来进行增删改查，而 MySQL 使用 SQL 语言进行上诉操作。

### 倒排索引

### ES 优化写入性能

- 用SSD
- 多线程bulk
- 尽量设置每个bulk的大小在5~15M左右
- 增加节点、分片
- 设置多个path.data目录，或配置RAID 0阵列
- 如果用的是SSD，设置index.store.throttle.type：none
- 禁用_all
- 增大index.refresh_interval的值，默认1s
- 增大index.translog.flush_threshold_size的值
- 设置0副本，建完索引优化后再增加副本
- 增大indices.memory.index_buffer_size的值
- 用比较新版本的ES

### ES cluster
- node.master：表示节点是否具有称为主节点的资格

　　- true代表的是有资格竞选主节点

　　- false代表的是没有资格竞选主节点

- node.data：表示节点是否存储数据

#### 部署单节点
-  123
-  123
-  213
-  234
-  2

#### 配置文件
##### 3个节点配置如下:
node-1:

    # ---------------------------------- Cluster -----------------------------------
    #
    # Use a descriptive name for your cluster:
    cluster.name: es-cluster
    #
    # ------------------------------------ Node ------------------------------------
    #
    node.name: node-1
    node.master: true
    node.data: true
    #
    # ----------------------------------- Paths ------------------------------------
    #
    path.logs: /home/share/es-logs
    #
    # ---------------------------------- Network -----------------------------------
    #
    # Set a custom port for HTTP:
    #
    http.port: 9200
    transport.port: 9300
    #
    # --------------------------------- Discovery ----------------------------------
    #
    # Pass an initial list of hosts to perform discovery when this node is started:
    # The default list of hosts is ["127.0.0.1", "[::1]"]
    discovery.seed_hosts: ["192.168.1.161:9300", "192.168.1.162:9300", "192.168.1.163:9300"]
    # Bootstrap the cluster using an initial set of master-eligible nodes:
    cluster.initial_master_nodes: ["node-1"]
    #discovery.type: single-node
    #
    # ---------------------------------- Gateway -----------------------------------
    #
    # Block initial recovery after a full cluster restart until N nodes are started:
    #
    #gateway.recover_after_nodes: 3
    #
    # For more information, consult the gateway module documentation.
    #
    # ---------------------------------- Various -----------------------------------
    xpack.security.enabled: true
    xpack.security.transport.ssl.enabled: true
    xpack.security.transport.ssl.verification_mode: certificate
    xpack.security.transport.ssl.keystore.path: elastic-certificates.p12
    xpack.security.transport.ssl.truststore.path: elastic-certificates.p12
node-2:

    # ---------------------------------- Cluster -----------------------------------
    #
    # Use a descriptive name for your cluster:
    cluster.name: es-cluster
    #
    # ------------------------------------ Node ------------------------------------
    #
    node.name: node-2
    node.master: true
    node.data: true
    #
    # ----------------------------------- Paths ------------------------------------
    #
    path.logs: /home/share/es-logs
    #
    # ---------------------------------- Network -----------------------------------
    #
    # Set a custom port for HTTP:
    #
    http.port: 9200
    transport.port: 9300
    #
    # --------------------------------- Discovery ----------------------------------
    #
    # Pass an initial list of hosts to perform discovery when this node is started:
    # The default list of hosts is ["127.0.0.1", "[::1]"]
    discovery.seed_hosts: ["192.168.1.161:9300", "192.168.1.162:9300", "192.168.1.163:9300"]
    # Bootstrap the cluster using an initial set of master-eligible nodes:
    # cluster.initial_master_nodes: ["node-1"]
    #discovery.type: single-node
    #
    # ---------------------------------- Gateway -----------------------------------
    #
    # Block initial recovery after a full cluster restart until N nodes are started:
    #
    #gateway.recover_after_nodes: 3
    #
    # For more information, consult the gateway module documentation.
    #
    # ---------------------------------- Various -----------------------------------
    xpack.security.enabled: true
    xpack.security.transport.ssl.enabled: true
    xpack.security.transport.ssl.verification_mode: certificate
    xpack.security.transport.ssl.keystore.path: elastic-certificates.p12
    xpack.security.transport.ssl.truststore.path: elastic-certificates.p12
    
node-3:

    # ---------------------------------- Cluster -----------------------------------
    #
    # Use a descriptive name for your cluster:
    cluster.name: es-cluster
    #
    # ------------------------------------ Node ------------------------------------
    #
    node.name: node-3
    node.master: true
    node.data: true
    #
    # ----------------------------------- Paths ------------------------------------
    #
    path.logs: /home/share/es-logs
    #
    # ---------------------------------- Network -----------------------------------
    #
    # Set a custom port for HTTP:
    #
    http.port: 9200
    transport.port: 9300
    #
    # --------------------------------- Discovery ----------------------------------
    #
    # Pass an initial list of hosts to perform discovery when this node is started:
    # The default list of hosts is ["127.0.0.1", "[::1]"]
    discovery.seed_hosts: ["192.168.1.161:9300", "192.168.1.162:9300", "192.168.1.163:9300"]
    # Bootstrap the cluster using an initial set of master-eligible nodes:
    # cluster.initial_master_nodes: ["node-1"]
    # discovery.type: single-node
    #
    # ---------------------------------- Gateway -----------------------------------
    #
    # Block initial recovery after a full cluster restart until N nodes are started:
    #
    #gateway.recover_after_nodes: 3
    #
    # For more information, consult the gateway module documentation.
    #
    # ---------------------------------- Various -----------------------------------
    xpack.security.enabled: true
    xpack.security.transport.ssl.enabled: true
    xpack.security.transport.ssl.verification_mode: certificate
    xpack.security.transport.ssl.keystore.path: elastic-certificates.p12
    xpack.security.transport.ssl.truststore.path: elastic-certificates.p12
#### Node组合

- 主节点+数据节点(master+data)  
	- 节点即有称为主节点的资格，又存储数据

		node.master: true
		node.data: true

- 数据节点(data)
　	- 节点没有成为主节点的资格，不参与选举，只会存储数据  

		node.master: false
		node.data: true
	
- 客户端节点(client)
　　- 不会成为主节点，也不会存储数据，主要是针对海量请求的时候，可以进行负载均衡

		node.master: false
		node.data: false
		


#### 广度优先vs深度优先

## 测试结果
_功能点:多条件多字段分组聚合统计_

### 第一周

|方案|数据量|存储方式|内存|cpu|节点数量|存储|结果|
|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
|一|5400w|ElasticSearch|8g|32core|  单机单实例 |机械硬盘| 耗时 9s 左右|
|二|2000w  | mongodb | 8g |32core | 单机单实例  |机械硬盘|耗时25-35s|  

结论:
  
- 单机情况下, 无任何优化   多条件分组聚合统计效率低下
- 存储方式为机械硬盘, 对查询效率有较大影响
- 目标: 十亿级别数据量, 对标当前需求, 3s查询得到结果

针对当前目标及现有资源, 做如下优化:

- 增加节点,分片
- 增加内存
- 存储选择:SSD
- ES参数调优及索引调优
- 代码优化: 根据ES官方可选属性, 针对当前业务场景选择不同维度的查询等
-----------------------------------

### 第二周
#### ES
|节点|数据量|索引|内存|cpu|存储|度量字段数|结果|
|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
|1node|2000w|单索引|8g|32core |机械硬盘|2| 4s|
|1node|2000w|单索引|8g|32core |机械硬盘|7| 9s|
|3node|2000w |单索引|8g*3 |32core|机械硬盘|7|8s|  

结论:
- 首先增加了2个节点,总共3个节点  做7个字段的分组聚合 得到目标数据耗时几户相等
- 增加内存  聚合效率相当  内存过大会OOM
- 代码优化: 采用breath_first和depth_first   大数据量效率相当

待做:
- ES参数调优及索引调优
- 存储方式
- cpu/memory

#### Mongo

- 环境：服务器两台，搭建一个主节点，5个分片（一台3个分片，一台2个分片）
- 查询结果：两个维度，八个指标聚合数据：1000W  5~6s

### 第三周
