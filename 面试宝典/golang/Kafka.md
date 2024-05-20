## Kafka

### Kafka 是什么？主要应用场景有哪些？

Kafka 是一个分布式流式处理平台。

流平台具有三个关键功能：

```
消息队列：发布和订阅消息流，这个功能类似于消息队列，这也是 Kafka 也被归类为消息队列的原因。
容错的持久方式存储记录消息流：Kafka 会把消息持久化到磁盘，有效避免了消息丢失的⻛险。
流式处理平台：在消息发布的时候进行处理，Kafka 提供了一个完整的流式处理类库。
```
Kafka 主要有两大应用场景：

```
消息队列：建立实时流数据管道，以可靠地在系统或应用程序之间获取数据。
数据处理：构建实时的流数据处理程序来转换或处理数据流。
```
### 和其他消息队列相比，Kafka 的优势在哪里？

我们现在经常提到 Kafka 的时候就已经默认它是一个非常优秀的消息队列了，我们也会经常拿它跟 RocketMQ、
RabbitMQ 对比。我觉得 Kafka 相比其他消息队列主要的优势如下：

```
极致的性能：基于 Scala 和 Java 语言开发，设计中大量使用了批量处理和异步的思想，最高可以每秒处理千
万级别的消息。
生态系统兼容性无可匹敌：Kafka 与周边生态系统的兼容性是最好的没有之一，尤其在大数据和流计算领域。
```
实际上在早期的时候 Kafka 并不是一个合格的消息队列，早期的 Kafka 在消息队列领域就像是一个衣衫褴褛的孩子
一样，功能不完备并且有一些小问题比如丢失消息、不保证消息可靠性等等。当然，这也和 LinkedIn 最早开发
Kafka 用于处理海量的日志有很大关系，哈哈哈，人家本来最开始就不是为了作为消息队列滴，谁知道后面误打误
撞在消息队列领域占据了一席之地。

### 什么是 Producer、Consumer、Broker、Topic、 Partition？

Kafka 将生产者发布的消息发送到 Topic（主题）中，需要这些消息的消费者可以订阅这些 Topic（主题）。Kafka
比较重要的几个概念：

```
Producer（生产者）: 产生消息的一方。
Consumer（消费者）: 消费消息的一方。
Broker（代理）: 可以看作是一个独立的 Kafka 实例。多个 Kafka Broker 组成一个 Kafka Cluster。
Topic（主题）: Producer 将消息发送到特定的主题，Consumer 通过订阅特定的 Topic(主题)来消费消息。
Partition（分区）: Partition 属于 Topic 的一部分。一个 Topic 可以有多个 Partition ，并且同一 Topic 下的
Partition 可以分布在不同的 Broker 上，这也就表明一个 Topic 可以横跨多个 Broker 。这正如我上面所画的
图一样。
```

### Kafka 的多副本机制了解吗？

Kafka 为分区（Partition）引入了多副本（Replica）机制。分区

```
Partition）中的多个副本之间会有一个叫做 leader 的家伙，其他副本称为 follower。我们发送的消息会被发
送到 leader 副本，然后 follower 副本才能从 leader 副本中拉取消息进行同步。
```
生产者和消费者只与 leader 副本交互。你可以理解为其他副本只是 leader 副本的拷⻉，它们的存在只是为了保证
消息存储的安全性。当 leader 副本发生故障时会从 follower 中选举出一个 leader,但是 follower 中如果有和
leader 同步程度达不到要求的参加不了 leader 的竞选。

### Kafka 的多分区（Partition）以及多副本（Replica）机制有什么好处

### 呢？

```
Kafka 通过给特定 Topic 指定多个 Partition,而各个 Partition 可以分布在不同的 Broker 上,这样便能提供比较
好的并发能力（负载均衡）。
Partition 可以指定对应的 Replica 数,这也极大地提高了消息存储的安全性,提高了容灾能力，不过也相应的增
加了所需要的存储空间。
```
### Zookeeper 在 Kafka 中的作用知道吗？

```
Broker 注册：在 Zookeeper 上会有一个专⻔用来进行 Broker 服务器列表记录的节点。每个 Broker 在启动
时，都会到 Zookeeper 上进行注册，即到/brokers/ids 下创建属于自己的节点。每个 Broker 就会将自己的
IP 地址和端口等信息记录到该节点中去
Topic 注册：在 Kafka 中，同一个 Topic 的消息会被分成多个分区并将其分布在多个 Broker 上，这些分区信
息及与 Broker 的对应关系也都是由 Zookeeper 在维护。比如我创建了一个名字为 my-topic 的主题并且它有
两个分区，对应到 zookeeper 中会创建这些文件夹：/brokers/topics/my-
topic/Partitions/0、/brokers/topics/my - topic/Partitions/1
负载均衡：上面也说过了 Kafka 通过给特定 Topic 指定多个 Partition,而各个 Partition 可以分布在不同的
Broker 上,这样便能提供比较好的并发能力。对于同一个 Topic 的不同 Partition，Kafka 会尽力将这些
Partition 分布到不同的 Broker 服务器上。当生产者产生消息后也会尽量投递到不同 Broker 的 Partition 里
面。当 Consumer 消费的时候，Zookeeper 可以根据当前的 Partition 数量以及 Consumer 数量来实现动态
负载均衡。
```
### Kafka 如何保证消息的消费顺序？

#### 我们在使用消息队列的过程中经常有业务场景需要严格保证消息的消费顺序，比如我们同时发了 2 个消息，这 2 个

#### 消息对应的操作分别对应的数据库操作是：

#### 更改用户会员等级。

```
根据会员等级计算订单价格。假如这两条消息的消费顺序不一样造成的最终结果就会截然不同。 Kafka 中
Partition(分区)是真正保存消息的地方，我们发送的消息都被放在
```
了这里。而我们的 Partition(分区)又存在于 Topic(主题)这个概念中，并且我们可以给特定 Topic 指定多个
Partition。

每次添加消息到 Partition(分区)的时候都会采用尾加法，如上图所示。 Kafka 只能为我们保证 Partition(分区)中的
消息有序。

消息在被追加到 Partition(分区)的时候都会分配一个特定的偏移量


```
offset）。Kafka 通过偏移量（offset）来保证消息在分区内的顺序性。所以，我们就有一种很简单的保证消
息消费顺序的方法： 1 个 Topic 只对应一个 Partition。这样当然可以解决问题，但是破坏了 Kafka 的设计初
衷。 Kafka 中发送 1 条消息的时候，可以指定 topic, partition, key,data（数据） 4 个参数。如果你发送消息
的时候指定了 Partition 的话，所有消息都会被发送到指定的 Partition。并且，同一个 key 的消息可以保证只
发送到同一个 partition，这个我们可以采用表/对象的 id 来作为 key。
```
总结一下，对于如何保证 Kafka 中消息消费的顺序，有了下面两种方法：

```
1 个 Topic 只对应一个 Partition。
发送消息的时候指定 key/Partition。
```
### Kafka 如何保证消息不丢失？

#### 生产者丢失消息的情况

生产者(Producer)调用 send 方法发送消息之后，消息可能因为网络问题并没有发送过去。

所以，我们不能默认在调用 send 方法发送消息之后消息发送成功了。为了确定消息是发送成功，我们要判断消息
发送的结果。但是要注意的是 Kafka 生产者(Producer)使用 send 方法发送消息实际上是异步的操作，我们可以通

过 get()方法获取调用结果，但是这样也让它变为了同步操作。

**消费者丢失消息的情况**

我们知道消息在被追加到 Partition(分区)的时候都会分配一个特定的偏移量

```
offset）。偏移量（offset)表示 Consumer 当前消费到的 Partition(分区)的所在的位置。Kafka 通过偏移量
（offset）可以保证消息在分区内的顺序性。
```
当消费者拉取到了分区的某个消息之后，消费者会自动提交了 offset。自动提交的话会有一个问题，试想一下，当
消费者刚拿到这个消息准备进行真正消费的时候，突然挂掉了，消息实际上并没有被消费，但是 offset 却被自动提
交了。

解决办法也比较粗暴，我们手动关闭自动提交 offset，每次在真正消费完消息之后再自己手动提交 offset 。但是，
细心的朋友一定会发现，这样会带来消息被重新消费的问题。比如你刚刚消费完消息之后，还没提交 offset，结果
自己挂掉了，那么这个消息理论上就会被消费两次。

### Kafka 判断一个节点是否还活着有那两个条件？

```
节点必须可以维护和 ZooKeeper 的连接，Zookeeper 通过心跳机制检查每个节点的连接；
如果节点是个 follower,他必须能及时的同步 leader 的写操作，延时不能太久。
```
### producer 是否直接将数据发送到 broker 的 leader（主节点）？

producer 直接将数据发送到 broker 的 leader(主节点)，不需要在多个节点进行分发，为了

帮助 producer 做到这点，所有的 Kafka 节点都可以及时的告知:哪些节点是活动的，目标

topic 目标分区的 leader 在哪。这样 producer 就可以直接将消息发送到目的地了。


### Kafka consumer 是否可以消费指定分区消息吗？

Kafa consumer 消费消息时，向 broker 发出"fetch"请求去消费特定分区的消息，consumer 指定消息在日志中的
偏移量（offset），就可以消费从这个位置开始的消息，customer 拥有了 offset 的控制权，可以向后回滚去重新
消费之前的消息，这是很有意义的。

### Kafka 高效文件存储设计特点是什么？

```
Kafka 把 topic 中一个 parition 大文件分成多个小文件段，通过多个小文件段，就容易定期清除或删除已经消
费完文件，减少磁盘占用。
通过索引信息可以快速定位 message 和确定 response 的最大大小。
通过 index 元数据全部映射到 memory，可以避免 segment file 的 IO 磁盘操作。
通过索引文件稀疏存储，可以大幅降低 index 文件元数据占用空间大小。
```
### partition 的数据如何保存到硬盘？

topic 中的多个 partition 以文件夹的形式保存到 broker，每个分区序号从 0 递增，且消息有序。

Partition 文件下有多个 segment（xxx.index，xxx.log）

segment 文件里的大小和配置文件大小一致可以根据要求修改，默认为1g。如果大小大于1g 时，会滚动一个新的
segment 并且以上一个 segment 最后一条消息的偏移量命名。

### kafka 生产数据时数据的分组策略是怎样的？

生产者决定数据产生到集群的哪个 partition 中，每一条消息都是以（key， value）格式，Key 是由生产者发送数
据传入，所以生产者（key）决定了数据产生到集群的哪个 partition。

### consumer 是推还是拉？

customer 应该从 brokes 拉取消息还是 brokers 将消息推送到 consumer，也就是 pull 还 push。在这方面，
Kafka 遵循了一种大部分消息系统共同的传统的设计：producer 将消息推送到 broker，consumer 从 broker 拉
取消息。 push 模式，将消息推送到下游的 consumer。这样做有好处也有坏处：由 broker 决定消息推送的速
率，对于不同消费速率的 consumer 就不太好处理了。消息系统都致力于让 consumer 以最大的速率最快速的消
费消息，但不幸的是，push 模式下，当 broker 推送的速率远大于 consumer 消费的速率时， consumer 恐怕就
要崩溃了。最终 Kafka 还是选取了传统的 pull 模式。

### kafka 维护消费状态跟踪的方法有什么？

大部分消息系统在 broker 端的维护消息被消费的记录：一个消息被分发到 consumer 后 broker 就⻢上进行标记
或者等待 customer 的通知后进行标记。这样也可以在消息在消费后立⻢就删除以减少空间占用。

### 是什么确保了 Kafka 中服务器的负载平衡？

#### 由于领导者的主要⻆色是执行分区的所有读写请求的任务，而追随者被动地复制领导者。因 此，在领导者失败时，

#### 其中一个追随者接管了领导者的⻆色。基本上，整个过程可确保服务 器的负载平衡。


### 消费者 API 的作用是什么？

#### 允许应用程序订阅一个或多个主题并处理生成给它们的记录流的 API，我们称之为消费者 API。

### 解释流 API 的作用？

#### 一种允许应用程序充当流处理器的 API，它还使用一个或多个主题的输入流，并生成一个输 出流到一个或多个输出

#### 主题，此外，有效地将输入流转换为输出流，我们称之为流 API。

### Kafka 为什么那么快?

```
Cache Filesystem Cache PageCache 缓存。
顺序写：由于现代的操作系统提供了预读和写技术，磁盘的顺序写大多数情况下比随机
```
写内存还要快。

```
Zero-copy 零拷技术减少拷⻉次数。
Batching of Messages 批量处理。合并小的请求，然后以流的方式进行交互，直顶网
```
络上限。

```
Pul- 拉模式 使用拉模式进行消息的获取消费，与消费端处理能力相符。
```
### Kafka 系统工具有哪些类型？

```
Kafka 迁移工具：它有助于将代理从一个版本迁移到另一个版本。
Mirror Maker：Mirror Maker 工具有助于将一个 Kafka 集群的镜像提供给另一个。 - 消费者检查:对于指定的
主题集和消费者组，它显示主题，分区，所有者。
```
### partition 的数据如何保存到硬盘

topic 中的多个 partition 以文件夹的形式保存到 broker，每个分区序号从 0 递增， 且消息有序。Partition 文
件下有多个 segment（xxx.index，xxx.log），segment 文件里 的大小和配置文件大小一致可以根据要求修改默
认为 1g。如果大小大于 1g 时，会滚动一 个新的 segment 并且以上一个 segment 最后一条消息的偏移量命
名。

### Zookeeper 对于 Kafka 的作用是什么？

```
Zookeeper 是一个开放源码的、高性能的协调服务，它用于 Kafka 的分布式应用。
Zookeeper 主要用于在集群中不同节点之间进行通信。
在 Kafka 中，它被用于提交偏移量，因此如果节点在任何情况下都失败了，它都可以从 之前提交的偏移量中
获取。
除此之外，它还执行其他活动，如: leader 检测、分布式同步、配置管理、识别新节点 何时离开或连接、集
群、节点实时状态等等。
```

### 流 API 的作用是什么？

#### 一种允许应用程序充当流处理器的 API，它还使用一个或多个主题的输入流，并生成一个输 出流到一个或多个输出

#### 主题，此外，有效地将输入流转换为输出流，我们称之为流 API。

### Kafka 的流处理是什么意思？

连续、实时、并发和以逐记录方式处理数据的类型，我们称之为 Kafka 流处理。

### Kafka 集群中保留期的目的是什么？

保留期限保留了 Kafka 群集中的所有已记录。它不会检查它们是否已被消耗。此外，可以通 过使用保留期的配置
设置来丢弃记录，而且，它可以释放一些空间。