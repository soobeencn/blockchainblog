# Elastic Stack
- 100% 开源  
- kibana+elasticsearch+logstash/beats

## Kibana
Kibana 是通向 Elastic 产品集的窗口， 
它可以在 Elasticsearch 中对数据进行视觉探索和实时分析
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
        - kabana.yml 打开注释，修改默认配置：
            > elasticsearch.username: "kibana"  
              elasticsearch.password: "123456"
          
```
    超级管理员：
    elastic
    admin123

    apm
    admin123

    kibana
    kibana123

    ～
    ～123
```
          
* 常用管理插件
    - stack monitoring
    - APM
    - discovery

## Logstash
Logstash 是 Elastic Stack 的中央数据流引擎，
用于收集、丰富和统一所有数据，而不管格式或模式。
当与Elasticsearch，Kibana，及 Beats 共同使用的时候便会拥有特别强大的实时处理能力。

### Logstash入门
_the dataflow engine_
- 开源的流式 **ETL** 引擎
- 几分钟即可构建 **数据流** 处理管道
- 具有自适应缓冲的 **水平伸缩性** 和 **弹性**
- 数据来源中立性， 支持 **众多数据源**
- 丰富的周边插件， 目前200多个的第三方集成和处理器
- Elastic Stack自带的**监控**和**管理**

### 接入指南

- 获取资源
    > curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.10.2-linux-x86_64.tar.gz

- 解压
    > tar zxvf filebeat-7.10.2-linux-x86_64.tar.gz

- 修改配置文件
    > vim filebeat.yml

- 加载列表信息
    > ./filebeat modules list

- 自定义加载关联组件
    > ./filebeat modules enable mongodb system nginx kafka mysql

- 自动配置assets信息
    > ./filebeat setup -e  
  
    _-e is optional and sends output to standard error instead of the configured log output._
- 启动
    > ./filebeat -e
  
- 查看日志  
  > 如果没有异常信息，打开kibana -> discovery -> filebeat-* 
  
- 日志录入
    > 

- ***FAQ***  
  关于启动filebeat遇到的一些常见问题：
  - 使用非root用户启动， 应当先授权目标用户的文件 744权限，如果权限过高， 则会启动失败；
  - 使用root用户启动，则按照下方规则进行配置：
    > sudo chown root filebeat.yml  
      sudo chown root modules.d/system.yml  
      sudo ./filebeat -e
    
    








## Beats 【goland】
前端agent

## X-Pack plugin 需要商业授权
- Security
- Alerting
- Monitoring
- Reporting
- Graph
- Machine Language


            
