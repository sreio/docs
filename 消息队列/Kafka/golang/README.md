> Kafka是一种高吞吐量的分布式发布订阅消息系统，本文介绍了如何使用`kafka-go`这个库实现Go语言与kafka的交互。

Go社区中目前有三个比较常用的kafka客户端库 , 它们各有特点。

- [IBM/sarama](https://github.com/IBM/sarama)
  > 这个库已经由Shopify转给了IBM
- [kafka-go](https://github.com/segmentio/kafka-go)
  > kafka-go 更简单、更易用
  >
  > segmentio/kafka-go 是纯Go实现，提供了与kafka交互的低级别和高级别两套API，同时也支持Context。
- [confluent-kafka-go](https://github.com/confluentinc/confluent-kafka-go)
  > 一个基于cgo的librdkafka包装，在项目中使用它会引入对C库的依赖。

