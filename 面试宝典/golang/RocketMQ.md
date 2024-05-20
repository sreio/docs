## RocketMQ 面试题

### 多个 MQ 如何选型？

### 为什么要使用 MQ？

#### 因为项目比较大，做了分布式系统，所有远程服务调用请求都是同步执行经常出问题，所 以引入了 MQ。

### RocketMQ 由哪些⻆色组成，每个⻆色作用和特点是什么？


#### ⻆色 作用

```
Nameserver 无状态，动态列表；这是和 ZooKeeper 的重要区别之一。ZooKeeper 是有状态的。
```
```
Producer 消息生产者，负责发消息到 Broker。
```
```
Broker 就是 MQ 本身，负责收发消息、持久化消息等。
```
```
Consumer 消息消费者，负责从 Broker 上拉取消息进行消费，消费完进行 ack。
```
### RocketMQ 中的 Topic 和 JMS 的 queue 有什么区别？

queue 就是来源于数据结构的 FIFO 队列。而 Topic 是个抽象的概念，每个 Topic 底层对 应 N 个 queue，而数据
也真实存在 queue 上的。

### RocketMQ 消费模式有几种？

消费模型由 Consumer 决定，消费维度为 Topic。

```
集群消费
```
1.一条消息只会被同 Group 中的一个 Consumer 消费

2.多个 Group 同时消费一个 Topic 时，每个 Group 都会有一个 Consumer 消费到数据。

```
广播消费
```
消息将对一个 Consumer Group 下的各个 Consumer 实例都消费一遍。即即使这些Consumer 属于同一个
Consumer Group，消息也会被 Consumer Group 中的每个 Consumer 都消费一次。

### Broker 如何处理拉取请求的？

Consumer 首次请求 Broker。

```
Broker 中是否有符合条件的消息
有
响应 Consumer。
等待下次 Consumer 的请求。
没有
DefaultMessageStore#ReputMessageService#run 方法。 - PullRequestHoldService 来 Hold 连接，
每个 5s 执行一次检查pullRequestTable 有没有消息，有的话立即推送。
每隔 1ms 检查 commitLog 中是否有新消息，有的话写入到pullRequestTable。
当有新消息的时候返回请求。
挂起 consumer 的请求，即不断开连接，也不返回数据。
使用 consumer 的 offset。
```

### RocketMQ 如何做负载均衡？

通过 Topic 在多 Broker 中分布式存储实现。

**producer 端**

发送端指定 message queue 发送消息到相应的 broker，来达到写入时的负载均衡

```
提升写入吞吐量，当多个 producer 同时向一个 broker 写入数据的时候，性能会下降 - 消息分布在多 broker
中，为负载消费做准备
```
**默认策略是随机选择：**

```
producer 维护一个 index
每次取节点会自增
index 向所有 broker 个数取余
自带容错策略
```
**其他实现：**

```
SelectMessageQueueByHash
hash 的是传入的 args
SelectMessageQueueByRandom
SelectMessageQueueByMachineRoom 没有实现
```
也可以自定义实现 MessageQueueSelector 接口中的 select 方法

**consumer 端** 采用的是平均分配算法来进行负载均衡。

**其他负载均衡算法**

平均分配策略(默认)(AllocateMessageQueueAveragely) 环形分配策略
(AllocateMessageQueueAveragelyByCircle) 手动配置分配策略 (AllocateMessageQueueByConfig) 机房分配策
略 (AllocateMessageQueueByMachineRoom) 一致性哈希分配策略 (AllocateMessageQueueConsistentHash)
靠近机房策略 (AllocateMachineRoomNearby)

**追问：当消费负载均衡 consumer 和 queue 不对等的时候会发生什么？**

Consumer 和 queue 会优先平均分配，如果 Consumer 少于 queue 的个数，则会存在部 分 Consumer 消费多个
queue 的情况，如果 Consumer 等于 queue 的个数，那就是一 个 Consumer 消费一个 queue，如果 Consumer
个数大于 queue 的个数，那么会有部分 Consumer 空余出来，白白的浪费了。

### 消息重复消费

#### 影响消息正常发送和消费的重要原因是网络的不确定性。

#### 引起重复消费的原因

#### ACK

正常情况下在 consumer 真正消费完消息后应该发送 ack，通知 broker 该消息 已正常消费，从 queue 中剔除

#### MESSAGEQUEUE SELECT(FINAL LIST<MESSAGEQUEUE> MQS, FINAL MESSAGE MSG, FINAL OBJECT ARG);


当 ack 因为网络原因无法发送到 broker，broker 会认为词条消息没有被消费， 此后会开启消息重投机制把消息再
次投递到 consumer

```
消费模式
```
在 CLUSTERING模式下，消息在 broker 中会保证相同 group 的 consumer 消 费一次，但是针对不同 group 的
consumer 会推送多次

**解决方案**

```
数据库表
```
处理消息前，使用消息主键在表中带有约束的字段中 insert

```
Map
```
单机时可以使用 map ConcurrentHashMap -> putIfAbsent guava cache - Redis

分布式锁搞起来。

### RocketMQ 如何保证消息不丢失

#### 首先在如下三个部分都可能会出现丢失消息的情况：

```
Producer 端
Broker 端
Consumer 端
```
### Producer 端如何保证消息不丢失

```
采取 send()同步发消息，发送结果是同步感知的。
发送失败后可以重试，设置重试次数。默认 3 次。 PRODUCER.SETRETRYTIMESWHENSENDFAILED(10);
```
集群部署，比如发送失败了的原因可能是当前 Broker 宕机了，重试的时候会发

送到其他 Broker 上。

### Broker 端如何保证消息不丢失

#### 修改刷盘策略为同步刷盘。默认情况下是异步刷盘的。 FLUSHDISKTYPE = SYNC_FLUSH

#### 集群部署，主从模式，高可用。

### Consumer 端如何保证消息不丢失

```
完全消费正常后在进行手动 ack 确认。
```
### 高吞吐量下如何优化生产者和消费者的性能?

#### 开发

```
同一 group 下，多机部署，并行消费 - 单个 Consumer 提高消费线程个数 - 批量消费
消息批量拉取
业务逻辑批量处理
```
**运维**


#### 网卡调优

```
jvm 调优
多线程与 cpu 调优 - Page Cache
```