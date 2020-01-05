Hbase 入门
=========================

## HBase的应用场景和特点

* 业务场景:交通、金融(交易)、电商、移动(电话,短信,)

* 特点

    - 容量:海量数据存储    单表的存储数据量   数十亿行    上百万列(没有列的限制)   而普通关系型数据库单表存储一般不超过500万条数据,超出之后需要分表分库;

    - 面向列:HBase是面向列的存储和权限控制,并支持独立检索.列式存储,其数据在表中是按照某列存储的,这样在查询只需要少数几个字段的时候,能大大减少读取的数据量;

    - 多版本:HBase每一列支持多版本存储;

    - 稀疏性:为空的列并不占用存储空间,表可以设计的非常稀疏;

    - 扩展性:底层依赖于HDFS    动态增加节点机器;

    - 高可靠性:WAL机制保证了数据写入时不会因集群异常而导致写入数据丢失-replication机制保证了在集群出现严重的问题时,数据不会发生丢失或损坏.并且HBase底层使用HDFS,其也有备份;

    - 高性能:

        - 底层的LSM数据结构和Rowkey有序排列等架构上的独特设计,使HBase具备非常高的写入性能;

        - region切分、主键索引和缓存机制使HBase在海量数据下具备一定的随机读取性能,该性能针对Rowkey的查询能够到达ms级别.    

    - 准实时查询



## HBase的概念与定位

* hbase.apache.org

* 选择合适HBase的版本

    - 官方:http://archive.apache.org/dist/hbase/

    - CDH:http://archive.cloudera.com/cdh5/

* Hadoop2.x生态系统中的定位



## HBase架构体系

 


## HBase设计模型

 * 表设计  存在列蔟   并不存在实际的列信息    

    - 一张表列蔟不会超过5个   建表的时候就会确认   

    - 每个列蔟中的列数没有限制

    - 列只有插入数据后存在

    - 列在列蔟中有序排列



## HBase对比关系型数据库
    - 列动态增加
    - 数据自动切分
    - 高并发读写
    - 缺点:不支持条件查询
## HBase的安装部署

    - JDK1.7以上

    - Hadoop-2.5.0以上

    - zookeeper-3.4.5以上

    - HDFS

* Hadoop2.x分布式安装配置

- 解压Hadoop-2.5.0并安装

- 配置hadoop-env.sh


## HBase shell使用

- Create

- Describe

- is_enabled

- is_disabled

- Drop

- Enable

- Disable

- List

- Count

- Delete

- get

- Put

- Scan

- truncate

## 总结

```



```

