# kafka-基础

##### kafka-介绍

- 背景

- **Kafka**是最初由Linkedin公司开发，是一个分布式、支持分区的（partition）、多副本的（replica），基于zookeeper协调的分布式消息系统，它的最大的特性就是可以实时的处理大量数据以满足各种需求场景：比如基于hadoop的批处理系统、低延迟的实时系统、storm/Spark流式处理引擎，web/nginx日志、访问日志，消息服务等等，用scala语言编写，Linkedin于2010年贡献给了Apache基金会并成为顶级开源 项目。

- 场景

- 日志收集

- 消息系统

- 用户活动跟踪

##### kafka-安装

- 安装zookeeper

- 下载kafka安装包

- [Apache Kafka](https://kafka.apache.org/downloads)

- 上传到指定目录后解压

- 配置文件 /data/www/kakfa/kafka_2.11-2.4.0/config/server.properties

\#kakfa 的broker-id

broker.id=0

\#监听的端口 

listeners=PLAINTEXT://192.168.1.15:9092

\#监听的端口 与上面保持一致

advertised.listeners=PLAINTEXT://192.168.1.15:9092

\#配置log.dirs的配置文件 kafka的数据是以日志的形式存在的

log.dirs=/data/www/kafka/data/kafka-logs

- 日志文件

![img](https://vipkshttps6.wiz.cn/editor/389f1850-89b1-11ec-ba6c-21d96952d446/1b96da77-6d67-4e0e-ab63-16761295cd4f/resources/jETBTgEdoKQaYAoTr0s3Pv9a53W1Dx0Yogg-hslixRA.png?token=W.sOGtuJ70J3aUu5dMLtEXWAI2g-rkcVUR69dunklnwxnCZP6u8zN_eody6V4IAVM)

- __consumer_offsets

- kafka内部自己创建了_consumer_offset主题包含了50个分区。这个主题用来存放消费者消费某个主题的偏移量

- 消费者在消费的过程中，会定期将自己的消费分区的offset提交给kafka内部的topic,consumer_offset,提交过去的时候，key是consumerGoupId+topic+分区，value就是当前offset的值，kafka 会定期清理topic里面的信息，最后就保留最新的那条数据。

- 因为_consumer_offsets可能会接收高并发的请求，kafka默认给其分配50个分区，（可以通过offsets.topic.num.partitions 设置）这样可以通过增加机器的方式，扛大并发

- 通过如下公式可以计算出consumer消费的offset要提交到_consumer_offsets的哪个分区

- 公式 hash（consumerGroupId）%_consumer_offsets主题的分区数

-  my-repliacate-topic 

- 用户自建的主题，-0和-1表示的是这个主题的分区

- 里面的000000.log就消息的具体内容，默认保存7天。

- 启动

- 单机启动

\# 启动脚本 后台启动 加载配置文件

./kafka-server-start.sh -daemon ../config/server.properties

- 集群启动

- 修改配置文件 对应的都要进行修改调整

\#kakfa 的broker-id

broker.id=0

\#监听的端口 

listeners=PLAINTEXT://192.168.1.15:9092

\#监听的端口 与上面保持一致

advertised.listeners=PLAINTEXT://192.168.1.15:9092

\#配置log.dirs的配置文件 kafka的数据是以日志的形式存在的

log.dirs=/data/www/kafka/data/kafka-logs

\# 启动脚本 后台启动 加载配置文件

./kafka-server-start.sh -daemon ../config/server.properties

./kafka-server-start.sh -daemon ../config/server1.properties

./kafka-server-start.sh -daemon ../config/server2.properties

- 通过查看zk上的节点 检测是否成功

ls /brokers/ids

##### kafka-概念

- 常用解释

| 名称             | 解释                                                         |
| ---------------- | ------------------------------------------------------------ |
| broker           | 消息中间件处理节点，一个kafka节点就是一个broker，一个或多个broker可以组件成为一个kafka集群 |
| topic            | kafka根据topic对消息进行归类，发布到kafka集群的每条消息都需要指定topic |
| producer         | 消息生产者，向broker发送消息的客户端                         |
| consumer         | 消息消费者，向broker消费消费的客户端                         |
| consumerGroup    | 每个consumer属于一个特定的consumerGroup，一条消息可以被不同的consumer group消费，但是一个consumerGroup只有一个consumer可以消费该消息 |
| partition        | 物理上的概念 ，一个topic可以分为多个partition，每个partition内部消息是有序的 |
| replication      | 副本是对分区的备份。在集群中，不同的副本会被部署在不同的topic上。 |
| controller(集群) | 控制器组件（Controller），是 Apache Kafka 的核心组件。它的主要作用是在 Apache ZooKeeper 的帮助下管理和协调整个 Kafka 集群 |
| rebalance(集群)  | 某个消费组在消费的途中有消费者宕机或者有新的消费者加入的时候那么partition分配就是不公平的，可能导致某些消费者负载特别重，某些消费者又没有负载的情况。rebalane机制处理这种情况 |
| HW 高水位-集群   | 俗称高水位，它标识了一个特定的消息偏移量（offset），消费者只能拉取到这个 offset 之前的消息。 |

- topic

- 主题topic在kafka中是一个逻辑的概念，kafka通过topic将消息进行分类。不同的topic会被订阅该topic的消费者消费。

- 因为kafka的消息是会以日志的形式存在于log中，解决日志过大的问题，kafka提出了分区的概念

- partition

- 一个主题消息量时非常巨大的，因此可以通过分区的设置，来分布式存储这些消息。比如一个topic创建了3个分区。那么topic中的消息就会被分别存放在这三个分区中。

- 优势

- 分区存储，可以解决统一存储文件过大的问题

- 提高了读写的吞吐量: 读和写可以同时在多个分区中进行

![img](https://vipkshttps6.wiz.cn/editor/389f1850-89b1-11ec-ba6c-21d96952d446/1b96da77-6d67-4e0e-ab63-16761295cd4f/resources/IM28J6KQ_BvxYMOWvdkvZvL0RogfE2KeenEunS9Q9O0.svg?token=W.sOGtuJ70J3aUu5dMLtEXWAI2g-rkcVUR69dunklnwxnCZP6u8zN_eody6V4IAVM)

- replication

- 副本是对分区的备份

- 创建多分区多副本的主题

./kafka-topics.sh --create --zookeeper 192.168.1.15:2181 --replication-factor 3 --partitions 2 --topic my-repliacate-topic

- 通过top查看集群中的主题信息

./kafka-topics.sh --describe --zookeeper 192.168.1.15:2181 --topic my-t-repliacate-topic

![img](https://vipkshttps6.wiz.cn/editor/389f1850-89b1-11ec-ba6c-21d96952d446/1b96da77-6d67-4e0e-ab63-16761295cd4f/resources/_2RcGB1pTefqd5gAERzcsECsYVsS_pEA_-Xd7jTduDQ.png?token=W.sOGtuJ70J3aUu5dMLtEXWAI2g-rkcVUR69dunklnwxnCZP6u8zN_eody6V4IAVM)

- 补充说明

- replicas:

- 当前副本存在的broker节点

- leader：

- 副本里的概念

- 每个partition都有一个broker作为leader

- 消息发送方要把消息发送给哪个broker？取决于leader在哪个broker上，副本里面的broker专门用来接收消息

- 接收到消息，其他follower通过poll方式来同步数据

- follower：

- leader处理所有针对这个partition的读写请求。而follower被动复制leader。不提供读写(主要是为了保证多副本数据与消费的一致性)，如果leader所在的broker挂掉，那么就会进行新的leader选举。

- isr：

- 可以同步的broker节点和已同步的broker节点，存放在isr集合中 如果isr节点中的性能较差，会被踢出节点。

- controller

- rebalance

- HW

- 通信模型

![img](https://vipkshttps6.wiz.cn/editor/389f1850-89b1-11ec-ba6c-21d96952d446/1b96da77-6d67-4e0e-ab63-16761295cd4f/resources/rr_-KRuy_xspDldc-PP_wqxHvjIHTsXI2BNHb0wQvbg.svg?token=W.sOGtuJ70J3aUu5dMLtEXWAI2g-rkcVUR69dunklnwxnCZP6u8zN_eody6V4IAVM)

- 服务端和客户端的通信是通过tcp协议来完成的

- zk的作用

- kafka集群是无状态的，kafka的节点信息，都是存储在zk上的

##### kafka-命令行操作

- 创建主题

\# 创建一个test的主题，它的分区是1,它的备份是1

./kafka-topics.sh --create --zookeeper 192.168.1.15:2181 --replication-factor 1 --partitions 1 --topic test

- 查看主题 (也可以直接看zk下面的kafka节点信息)

./kafka-topics.sh --list --zookeeper 192.168.1.15:2181

- 发送消息

\# test 代表刚才创建的主题 执行完命令后 会创建连接，每一行会作为一条信息

 ./kafka-console-producer.sh --broker-list 192.168.1.15:9092--topic test

- 消费消息

- kafka同样携带了一个命令行客户端，会将获取到的内容在命令中进行输出，默认是消费最新的消息。使用kafka的消费者消息的客户端，从指定kafka服务器的指定topic中消费消息

- 方式一 ：最后一条消息的偏移量+1开始消费

 ./kafka-console-consumer.sh --bootstrap-server 192.168.1.15:9092 --topic test

- 方式二 : --from-beginning 代表从头开始消费

./kafka-console-consumer.sh --bootstrap-server 192.168.1.15:9092 --from-beginning --topic test

- 发送消费的模型

![img](https://vipkshttps6.wiz.cn/editor/389f1850-89b1-11ec-ba6c-21d96952d446/1b96da77-6d67-4e0e-ab63-16761295cd4f/resources/aRqTWG49NH71XaZU_q1hVZ8GalGdrgcVnpj_XMpRBnk.svg?token=W.sOGtuJ70J3aUu5dMLtEXWAI2g-rkcVUR69dunklnwxnCZP6u8zN_eody6V4IAVM)

- 注意点

- 消息会被存储

- /data/www/kafka/data/kafka-los/test-0/0000000.log

- 消息是顺序存储的

- 消息是有偏移量的 通过offset来描述消息的有序性

- 消费时可以指明偏移量进行消费

- 单播消息

- 在一个kafka的topic中，启动两个消费者，一个生产者。生产者发送消息，这条消息不会被两个消费者消费。

- 如果多个消费者，在同一个消费组，那么只有一个消费者可以收到订阅的topic的消息。并且后启动的消费者会被默认消费topic。

- 开启消费组订阅

./kafka-console-consumer.sh --bootstrap-server 192.168.1.15:9092 --consumer-property group.id=testgroup --topic test

- 多播消息

- 不同的消费组订阅同一个topic，那么不同的消费组只有一个消费者能收到消息，实际上也是多个消费组的多个消费者收到了同一个消息。

\# 开启两个不同的消费组 每个消费组下面的消费者消费同一个主题

./kafka-console-consumer.sh --bootstrap-server 192.168.1.15:9092 --consumer-property group.id=testgroup --topic test

./kafka-console-consumer.sh --bootstrap-server 192.168.1.15:9092 --consumer-property group.id=testgroup1 --topic test

![img](https://vipkshttps6.wiz.cn/editor/389f1850-89b1-11ec-ba6c-21d96952d446/1b96da77-6d67-4e0e-ab63-16761295cd4f/resources/ymR1zCVHvHxOKQiAckXgKf3_a-YIw4xz057DUygFlGw.svg?token=W.sOGtuJ70J3aUu5dMLtEXWAI2g-rkcVUR69dunklnwxnCZP6u8zN_eody6V4IAVM)

- 消费组查看

- 查看有多少消费组

./kafka-consumer-groups.sh --bootstrap-server 192.168.1.15:9092 --list

- 消费组对应信息

./kafka-consumer-groups.sh --bootstrap-server 192.168.1.15:9092 --describe --group testgroup

![img](https://vipkshttps6.wiz.cn/editor/389f1850-89b1-11ec-ba6c-21d96952d446/1b96da77-6d67-4e0e-ab63-16761295cd4f/resources/JoU2C-QQy2jngBpxziNX8eKkEK9nZ7kkTddmzvRP3wM.png?token=W.sOGtuJ70J3aUu5dMLtEXWAI2g-rkcVUR69dunklnwxnCZP6u8zN_eody6V4IAVM)

- LAG:积压的消息数量

- LOG-END-OFFSET: 最后一条消息的偏移量

- CuRRENT-OFFSET: 当前消息的偏移量

- 集群

- 发送消息

 ./kafka-console-producer.sh --broker-list 192.168.1.15:9092 192.168.1.15:9093 192.168.1.15:9094 --topic my-t-repliacate-topic

- 接受消息（指定消费组）

 ./kafka-console-consumer.sh --bootstrap-server 192.168.1.15:9092 192.168.1.15:9093 192.168.1.15:9094 --from-beginning --consumer-property group.id=testGroup1topic --topic my-t-repliacate-topic

- 集群中的消费流程

![img](https://vipkshttps6.wiz.cn/editor/389f1850-89b1-11ec-ba6c-21d96952d446/1b96da77-6d67-4e0e-ab63-16761295cd4f/resources/atkO9_C0OmWhSVULT4_F4eoAiOAgp5SYKhjXIGR7C_A.svg?token=W.sOGtuJ70J3aUu5dMLtEXWAI2g-rkcVUR69dunklnwxnCZP6u8zN_eody6V4IAVM)

- 一个partition只能被一个消费组里的某一个消费者消费，从而保证消费顺序。

- kafka只在partition的范围内保证消息消费的局部顺序性。

- 不能在同一个topic中的多个partition保证总的消费顺序性。

- 一个消费者可以消费多个partition

- 消费组中消费者的数量不能比一个topic的partition数量多，否则多出来的消费者消费不到消息

- 如果消费组挂了，那么会触发rebalance 机制，让其他消费者来消费partition

##### kafka-代码实现

- java

- springboot