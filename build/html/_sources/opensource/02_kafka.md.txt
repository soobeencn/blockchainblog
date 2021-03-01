## kafka 快速接入

### Kafka作为消息系统

Kafka streams的概念与传统的企业消息系统相比如何？

传统的消息系统有两个模块: 队列 和 发布-订阅。 在队列中，消费者池从server读取数据，每条记录被池子中的一个消费者消费; 在发布订阅中，记录被广播到所有的消费者。两者均有优缺点。 队列的优点在于它允许你将处理数据的过程分给多个消费者实例，使你可以扩展处理过程。 不好的是，队列不是多订阅者模式的—一旦一个进程读取了数据，数据就会被丢弃。 而发布-订阅系统允许你广播数据到多个进程，但是无法进行扩展处理，因为每条消息都会发送给所有的订阅者。

消费组在Kafka有两层概念。在队列中，消费组允许你将处理过程分发给一系列进程(消费组中的成员)。 在发布订阅中，Kafka允许你将消息广播给多个消费组。

Kafka的优势在于每个topic都有以下特性—可以扩展处理并且允许多订阅者模式—不需要只选择其中一个.

Kafka相比于传统消息队列还具有更严格的顺序保证

传统队列在服务器上保存有序的记录，如果多个消费者消费队列中的数据， 服务器将按照存储顺序输出记录。 虽然服务器按顺序输出记录，但是记录被异步传递给消费者， 因此记录可能会无序的到达不同的消费者。这意味着在并行消耗的情况下， 记录的顺序是丢失的。因此消息系统通常使用“唯一消费者”的概念，即只让一个进程从队列中消费， 但这就意味着不能够并行地处理数据。

Kafka 设计的更好。topic中的partition是一个并行的概念。 Kafka能够为一个消费者池提供顺序保证和负载平衡，是通过将topic中的partition分配给消费者组中的消费者来实现的， 以便每个分区由消费组中的一个消费者消耗。通过这样，我们能够确保消费者是该分区的唯一读者，并按顺序消费数据。 众多分区保证了多个消费者实例间的负载均衡。但请注意，消费者组中的消费者实例个数不能超过分区的数量。

---------------------------------------------
### 下载

> tar -xzf kafka_2.11-1.0.0.tgz
> 
> cd kafka_2.11-1.0.0

### 启动服务器

Kafka 使用 ZooKeeper 如果你还没有ZooKeeper服务器，

你需要先启动一个ZooKeeper服务器。 

您可以通过与kafka打包在一起的便捷脚本来快速简单地创建一个单节点ZooKeeper实例。

    > bin/zookeeper-server-start.sh config/zookeeper.properties
    [2013-04-22 15:01:37,495] INFO Reading configuration from: config/zookeeper.properties (org.apache.zookeeper.server.quorum.QuorumPeerConfig)
       ...

现在启动Kafka服务器：

    > bin/kafka-server-start.sh config/server.properties  
    [2013-04-22 15:01:47,028] INFO Verifying properties (kafka.utils.VerifiableProperties)
    [2013-04-22 15:01:47,051] INFO Property socket.send.buffer.bytes is overridden to 1048576 (kafka.utils.VerifiableProperties)  
    ...

### 创建一个 topic

让我们创建一个名为“test”的topic，它有一个分区和一个副本：

> bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test

现在我们可以运行list（列表）命令来查看这个topic：



> bin/kafka-topics.sh --list --zookeeper localhost:2181
test

或者，您也可将代理配置为：在发布的topic不存在时，自动创建topic，而不是手动创建。

### 启动一个 consumer

Kafka 还有一个命令行consumer（消费者），将消息转储到标准输出。

> bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning   

    This is a message    
    This is another message  

如果您将上述命令在不同的终端中运行，那么现在就可以将消息输入到生产者终端中，并将它们在消费终端中显示出来。

所有的命令行工具都有其他选项；运行不带任何参数的命令将显示更加详细的使用信息。

### 设置多代理集群

到目前为止，我们一直在使用单个代理，这并不好玩。对 Kafka来说，单个代理只是一个大小为一的集群，除了启动更多的代理实例外，没有什么变化。 为了深入了解它，让我们把集群扩展到三个节点（仍然在本地机器上）。

首先，为每个代理创建一个配置文件 (在Windows上使用copy 命令来代替)：


> cp config/server.properties config/server-1.properties  

> cp config/server.properties config/server-2.properties  

现在编辑这些新文件并设置如下属性：

> config/server-1.properties:  

    broker.id=1   
    listeners=PLAINTEXT://:9093    
    log.dir=/tmp/kafka-logs-1

> config/server-2.properties:  

    broker.id=2  
    listeners=PLAINTEXT://:9094  
    log.dir=/tmp/kafka-logs-2  

broker.id属性是集群中每个节点的名称，这一名称是唯一且永久的。我们必须重写端口和日志目录，因为我们在同一台机器上运行这些，我们不希望所有的代理尝试在同一个端口注册，或者覆盖彼此的数据。

我们已经建立Zookeeper和一个单节点了，现在我们只需要启动两个新的节点：

> bin/kafka-server-start.sh config/server-1.properties &
...  

> bin/kafka-server-start.sh config/server-2.properties &
...  

现在创建一个副本为3的新topic：

> bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 3 --partitions 1 --topic my-replicated-topic  

Good，现在我们有一个集群，但是我们怎么才能知道那些代理在做什么呢？运行"describe topics"命令来查看：

> bin/kafka-topics.sh --describe --zookeeper localhost:2181 --topic my-replicated-topic  

> Topic:my-replicated-topic   PartitionCount:1    ReplicationFactor:3 Configs:  

> Topic: my-replicated-topic  Partition: 0    Leader: 1   Replicas: 1,2,0 Isr: 1,2,0  

以下是对输出信息的解释。第一行给出了所有分区的摘要，下面的每行都给出了一个分区的信息。因为我们只有一个分区，所以只有一行。

“leader”是负责给定分区所有读写操作的节点。每个节点都是随机选择的部分分区的领导者。
“replicas”是复制分区日志的节点列表，不管这些节点是leader还是仅仅活着。
“isr”是一组“同步”replicas，是replicas列表的子集，它活着并被指到leader。
请注意，在示例中，节点1是该主题中唯一分区的领导者。

我们可以在已创建的原始主题上运行相同的命令来查看它的位置：


> bin/kafka-topics.sh --describe --zookeeper localhost:2181 --topic test

    Topic:test  PartitionCount:1    ReplicationFactor:1 Configs:
    Topic: test Partition: 0    Leader: 0   Replicas: 0 Isr: 0

这没什么大不了，原来的主题没有副本且在服务器0上。我们创建集群时，这是唯一的服务器。

让我们发表一些信息给我们的新topic：

> bin/kafka-console-producer.sh --broker-list localhost:9092 --topic my-replicated-topic
...

    my test message 1
    my test message 2

现在我们来消费这些消息：

> bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --from-beginning --topic my-replicated-topic
...

    my test message 1
    my test message 2

让我们来测试一下容错性。 Broker 1 现在是 leader，让我们来杀了它：

> ps aux | grep server-1.properties  

    7564 ttys002    0:15.91 /System/Library/Frameworks/JavaVM.framework/Versions/1.8/Home/bin/java...
> kill -9 7564

在 Windows 上用:
> wmic process where "caption = 'java.exe' and commandline like '%server-1.properties%'" get processid

    ProcessId
    6016
> taskkill /pid 6016 /f

领导权已经切换到一个从属节点，而且节点1也不在同步副本集中了：

> bin/kafka-topics.sh --describe --zookeeper localhost:2181 --topic my-replicated-topic

    Topic:my-replicated-topic   PartitionCount:1    ReplicationFactor:3 Configs:
    Topic: my-replicated-topic  Partition: 0    Leader: 2   Replicas: 1,2,0 Isr: 2,0

不过，即便原先写入消息的leader已经不在，这些消息仍可用于消费：

> bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --from-beginning --topic my-replicated-topic
...

    my test message 1
    my test message 2



------------------------------------------------------------------
### kafka彻底删除topic及data

- step1:
    

    如果需要被删除topic 此时正在被程序 produce和consume，则这些生产和消费程序需要停止。

    因为如果有程序正在生产或者消费该topic，则该topic的offset信息一致会在broker更新。调用kafka delete命令则无法删除该topic。
    
    同时，需要设置 auto.create.topics.enable = false，默认设置为true。如果设置为true，则produce或者fetch 不存在的topic也会自动创建这个topic。这样会给删除topic带来很多意向不到的问题。
    
    所以，这一步很重要，必须设置auto.create.topics.enable = false，并认真把生产和消费程序彻底全部停止。

- step2：


    server.properties 设置 delete.topic.enable=true
    
    如果没有设置 delete.topic.enable=true，则调用kafka 的delete命令无法真正将topic删除，而是显示（marked for deletion）

- step3：


    调用命令删除topic：
    
    ./bin/kafka-topics  --delete --zookeeper 【zookeeper server:port】  --topic 【topic name】
    

- step4：


    删除kafka存储目录（server.properties文件log.dirs配置，默认为"/data/kafka-logs"）相关topic的数据目录。
    
    注意：如果kafka 有多个 broker，且每个broker 配置了多个数据盘（比如 /data/kafka-logs,/data1/kafka-logs ...），且topic也有多个分区和replica，则需要对所有broker的所有数据盘进行扫描，删除该topic的所有分区数据。

_一般而言，经过上面4步就可以正常删除掉topic和topic的数据。但是，如果经过上面四步，还是无法正常删除topic，则需要对kafka在zookeeer的存储信息进行删除._

**具体操作如下：**

（注意：以下步骤里面，kafka在zk里面的节点信息是采用默认值，如果你的系统修改过kafka在zk里面的节点信息，则需要根据系统的实际情况找到准确位置进行操作）

- step5：

    
    找一台部署了zk的服务器，使用命令：

    bin/zkCli.sh -server 【zookeeper server:port】

    登录到zk shell，然后找到topic所在的目录：ls /brokers/topics，找到要删除的topic，然后执行命令：
    
    rmr /brokers/topics/【topic name】
    
    即可，此时topic被彻底删除。
    
    如果topic 是被标记为 marked for deletion，则通过命令 ls /admin/delete_topics，找到要删除的topic，然后执行命令：
    
    rmr /admin/delete_topics/【topic name】

***备注***：

网络上很多其它文章还说明，需要删除topic在zk上面的消费节点记录、配置节点记录，比如：

    rmr /consumers/【consumer-group】
    
    rmr /config/topics/【topic name】

其实正常情况是不需要进行这两个操作的，如果需要，那都是由于操作不当导致的。比如step1停止生产和消费程序没有做，step2没有正确配置。也就是说，正常情况下严格按照step1 -- step5 的步骤，是一定能够正常删除topic的。

step6：

完成之后，调用命令：

    ./bin/kafka-topics.sh --list --zookeeper 【zookeeper server:port】

查看现在kafka的topic信息。正常情况下删除的topic就不会再显示。

但是，如果还能够查询到删除的topic，则重启zk和kafka即可。
