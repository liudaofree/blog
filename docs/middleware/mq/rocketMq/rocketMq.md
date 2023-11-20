# rocketMq-基础

### 官网

- [RocketMQ · 官方网站 | RocketMQ (apache.org)](https://rocketmq.apache.org/)

### 概念

- RocketMq是一个统一消息引擎，轻量级数据处理平台rocketMq是一款阿里巴巴开源的消息中间件，2016年11月28日，阿里巴巴向Apache软件基金会，捐赠了RocketMq，成为Apache孵化项目。2017年月25日，Apache宣布Rocketmq孵化成为Apache顶级项目，（TLP）成为国内首个互联网中间件在apache上的顶级项目

#### 消息(Message)

- 消息是指，消息系统所传输信息的物理载体，生产和消费数据的最小单位，每条消息必须属于一个主题。

#### 主题(Topic)

- topic表示一类消息的集合，每个主题包含若干条消息，每条消息只能属于一个主题，是rocketMq进行消息的订阅的基本单位

- topic:message 1:n message:topic 1:1

- 生产者可以同时发送多种topic消息；而一个消费者只对某种特定的topic感兴趣，即只可以订阅和消费一种topic的消息 producer:topic 1:n consumer:topic 1:1

#### 标签(Tag)

- 为消息设置的标签，用于同一主题下区分不同类型的消息。来自同一业务单元的消息，可以根据不同业务目的在同一主题下设置不同标签。标签能够有效的保持代码的清晰度和连贯性，并优化RocketMq提供的查询系统。消费者可以根据tag实现不同子主题的不同消费逻辑，实现更好的扩展性

#### 队列(queue)

- 存储消息的物理实体。一个Topic中可以包含多个Queue。每个queue中存放的就是该topic的消息。一个topic的Queue也被称为一个topic中的消息的分区。

- 一个Topic中的Queue中的消息只能被一个消费者组中的一个消费者消费。一个Queue中的消息不允许同一个消费者组中的多个消费者同时消费。

#### 消息标识(MessageId/Key)

- RocketMq中每个消息拥有唯一的MessageId，且可以携带具有业务标识的key，以方便对消息的查询。不过需要注意的是，messageId有两个：生产者send()消息时会自动生成一个MessageId(msgId),当消息到达broker后，Broker也会自动生成一个MessageId(offsetMsgId)。msgId，OffsetMsgId与Key都称为消息标识

- msgid: 由producer端生成，其生成规则为

- producerIp+进程pid+MessageClientIDSetter类的ClassLoader的hashcode+当前时间+automicInteger自增计数器

- offsetMsgId：由broker端生成，其生成规则为: brokerIp+物理分区的offset

- key：由用户指定的业务相关的唯一标识

![img](https://vipkshttps6.wiz.cn/editor/389f1850-89b1-11ec-ba6c-21d96952d446/dc4802c0-8733-4fb0-966c-6f414e2e9347/resources/20FKCusj1WbmKjlDYUWEzrWM2ZN9mcmlbtEymdDyAqg.svg?token=W.hTw18PFPJFWYH-XeHfgrD6Wrrr3oXq_W8TixRHjM4dd-jjQSgQmCJ9gN6wAf9iQ)

### 系统架构

#### producer

- 消息生产者，负责生产消息。producer通过MQ的负载均衡模块选择相应的broker集群队列进行消息投递，投递的过程支持快速失败并且低延迟

- RocketMq中的消息生产者都是以生产者组（producer group）的形式出现的。生产者组是同一类生产者的集合，这类producer发送相同topic类型的消息。一个生产者组可以同时发送多个主题的消息

#### consumer

- 消息消费者，负责消费消息。一个消息消费者会从broker服务器中获取到消息，并对消息进行相关业务处理

- rocketMq中的消息消费者都是以消费者组的（consumer group）的形式出现的。消费者组是同一类消费者的集合。这类consumer消费的是同一个topic类型的消息。消费者组使得在消息消费方面，实现负载均衡和容错的目标变得非常容易。

- 消费者组中consumer的数量应该小于等于等于topic的queue数量。如果超出queue数量，则多出的consumer将不能消费消息。

- 一个topic类型的消息可以被多个消费者组同时消费

- 消费者组只能消费一个topic消息，不能同时消费多个topic消息

- 一个消费者组中的消费者必须订阅完全相同的topic

![img](https://vipkshttps6.wiz.cn/editor/389f1850-89b1-11ec-ba6c-21d96952d446/dc4802c0-8733-4fb0-966c-6f414e2e9347/resources/sbgI80-ZLOJXc0UF0QZNaMO_-GlRdDozxYrIVMah6Dg.svg?token=W.hTw18PFPJFWYH-XeHfgrD6Wrrr3oXq_W8TixRHjM4dd-jjQSgQmCJ9gN6wAf9iQ)

#### Name Server

- nameServer是一个broker与Topic路由的注册中心，支持broker的动态注册与发现。有点类似与kafka的zookeeper。

- 功能

- Broker管理

- 接受broker集群的注册信息并且保存下来作为路由信息的基本数据；提供心跳检测机制，检测broker是否还存活。

- 路由信息管理

- 每个NameServer中都保存着broker集群的整个路由信息和用于客户端查询的队列信息。producer和consumer通过NameServer可以获取整个broker集群的路由信息，从而进行消息的投递和消费。

- 路由注册

- NameServer通常也是集群的方式部署，不过NameServer是无状态的，即NameServer集群中的个节点是无差异的，各节点相互不进行信息通讯。在broker节点启动时，轮询broker列表，与每个NameServer建立长链接，发起注册请求。在NameServer内部维护一个Broker列表，用来动态存储broker信息。

- Broker节点通过30秒一次的心跳包发送最新的信息上报给NameServer。信息中包含BrokerId,Broker地址，broker名称，broker所属集群名称等。NameServer收到心跳包之后，会更新心跳时间戳，记录这个broker的最新存活时间

- 路由剔除

- NameServer没有收到Broker心跳，NameServer可能会将其从Broker列表中剔除。

- NameServer有一个定时任务，每隔10秒就会扫描一次Broker表，查看每一个Broker的最新心跳时间戳距离当前时间是否超过了120秒，如果超过则会判定broker失效。将其从broker列表中剔除

- 优点

- NameServer集群搭建简单，扩容简单

- 对于broker而言，不能随便扩容。因为必须明确指出所有的NameServer的地址。否则的话NameServer对其将是不可见的

- 路由发现

- Rocketmq的路由采用的是pull模式。当topic路由信息发生变化时，NameServer不会主动推送给客户端。而是客户端定时拉取主题最新的路由。默认客户端每30秒会拉取一次最新的路由 

push模型：推送模型，其实时性较好，是一个“发布-订阅”模型。需要维护一个长连接。而长连接的维护是需要资源成本的。
适合的场景:
   实时性要求较高
  client数量不多，Server数据变化较频繁
poll模型：拉取模型。存在的问题，实时性较差
Long pull模型: 长轮询模型。是对pull模型和push模型的整合

- 客户端nameServer选择策略

- 客户端指的是Producer和Consumer

- 客户端在配置时必须写上NameServer集群的地址，客户端首先会产生一个随机数，然后再与NameServer节点数量取模，此时得到的就是所要连接的节点索引。然后就会进行连接。如果连接失败，则采用round-robin策略，逐个尝试着去连接其它点

- 先随机，在轮询

zookeeper Client是如何选择Zookeeper Server的
将配置文件的Zk server地址进行第一次shuffle，然后随机选择一个。这个选择出的一般都一个hostname。然后取到该hostname对应的所有ip，在对这些ip进行第二次shuffle，从shuffle的结果中取出第一个地址进行连接。
shuffle是洗牌的意思

#### broker

- 功能

- Broker充当着消息中转角色，负责存储消息，转发消息。Broker在RocketMq系统中负责接收并存储从生产队列发送来的消息。同时为消费者的拉取请求做准备。Broker同时也存储着消息相关的元数据，包括消费者组消费进度偏移offset，主题，队列等。

- 模块

![img](https://vipkshttps6.wiz.cn/editor/389f1850-89b1-11ec-ba6c-21d96952d446/dc4802c0-8733-4fb0-966c-6f414e2e9347/resources/TYuoSJ5Dl_qY7gozZnPwEXnqSwDODJ9Z3pEbXa7FGe4.svg?token=W.hTw18PFPJFWYH-XeHfgrD6Wrrr3oXq_W8TixRHjM4dd-jjQSgQmCJ9gN6wAf9iQ)

- Remoting Module：

- 整个Broker的实体，负责处理来自于Client端的请求。由以下模块构成

- Client Manager:

- 客户端管理器。负责接收，解析客户端（producer/consumer）请求，管理客户端。例如，维护Consumer的topic订阅消息

- Store Service:

- 存储服务。提供方便简单的API接口。处理消息存储到物理硬盘和消息查询的功能

- HA Service:

- 高可用服务，提供Master Broker和Slave Broker之间的数据同步功能

- Index Servcie:

- 索引服务。根据特定的Message key，对投递到Broker的信息进行索引服务，同时也提供根据message key对消息进行快速查询的功能

- 集群部署

![img](https://vipkshttps6.wiz.cn/editor/389f1850-89b1-11ec-ba6c-21d96952d446/dc4802c0-8733-4fb0-966c-6f414e2e9347/resources/X0-bwncL1zzAisGEoMJhrUJgi7Bjh9JkH_WO0Zs4G5M.svg?token=W.hTw18PFPJFWYH-XeHfgrD6Wrrr3oXq_W8TixRHjM4dd-jjQSgQmCJ9gN6wAf9iQ)

- broker节点集群是一个主从集群，即集群中具有master和Slave两种角色。Master负责处理读写操作请求，而Slave负责对Master中的数据进行备份。当master挂掉了，slave则会自动切换为master去工作。一个master可以包含多个slave，而一个slave只能隶属于一个master。Master和slave的对应关系是通过指定相同的brokerName不同的brokerId来确定的。BrokerId为0表示为Master，非0表示为slave。每个broker与NamServer集群中的所有节点建立长连接，定时注册topic信息到所有NameServer。

#### 工作流程

- 启动NameServer，NameServer启动后开始监听端口，等待broker，consumer，producer的链接。

- 启动broker时，broker会与所有的NameServer建立并保持长连接，然后每30秒向NameServer定时发送心跳包。

- 发送消息前，可以先创建Topic，创建topic需要指定该topic要存储在哪些broker上，通知broker和topic的关系也会写入到NameServer中。也可以发送消息的时候，自动创建topic

- producer发送消息，启动时先跟NameServer集群中的某一台建立场长连接，并从NameServer中获取路由信息。即当前发送的topic消息的queue队列与broker的地址(IP+port)的映射关系。然后根据算法策略从中选择一个queue。与队列所在的broker建立长连接从而向Broker发消息。在获取到路由信息后，Producer会首先将路由信息缓存到本地，再每30秒从NameServer中更新一次路由信息

- consumer消费信息与producer类似，启动时先跟NameServer集群中的某一台建立场长连接，并从NameServer中获取路由信息。然后根据算法策略从路由信息中获取到其所要消费的queue，然后直接跟Broker建立长连接，开始消费其中的消息。consumer在获取路由信息后，同样也会每30秒从NameServer更新一次路由信息。Consumer还会向broker发送心跳。以确保broker的存活状态

- Topic的创建模式

- 手动创建topic时，有两种模式

- 集群模式：该模式下创建的topic在该集群中，所有的broker中Queue数量是相同的。

- Broker模式：该模式下创建的topic在该集群中，所有的broker中Queue数量可以不同。

- 自动创建topic时，默认采用的是broker模式，会为每个broker默认创建4个queue。

- 读写队列

- 从物理上来讲 读/写队列是同一个队列。所以不存在读/写队列数据同步问题。读/写队列是逻辑上进行区分的概念。一般情况下,读/写队列数量是相同的。

- 读/写队列数量可以设置不同是为了更方便的缩容

- perm用于设置对当前创建topic的操作权限：2表示只写，4表示只读，6表示读写