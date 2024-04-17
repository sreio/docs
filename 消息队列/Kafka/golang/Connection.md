### Connection

[Conn](https://pkg.go.dev/github.com/segmentio/kafka-go#Conn) 类型是 kafka-go 包的核心。它代表与 Kafka broker之间的连接。基于它实现了一套与Kafka交互的低级别 API。

## 发送消息

下面是连接至Kafka之后，使用Conn发送消息的代码示例。

```go
// writeByConn 基于Conn发送消息
func writeByConn() {
	topic := "my-topic"
	partition := 0

	// 连接至Kafka集群的Leader节点
	conn, err := kafka.DialLeader(context.Background(), "tcp", "localhost:9092", topic, partition)
	if err != nil {
		log.Fatal("failed to dial leader:", err)
	}

	// 设置发送消息的超时时间
	conn.SetWriteDeadline(time.Now().Add(10 * time.Second))

	// 发送消息
	_, err = conn.WriteMessages(
		kafka.Message{Value: []byte("one!")},
		kafka.Message{Value: []byte("two!")},
		kafka.Message{Value: []byte("three!")},
	)
	if err != nil {
		log.Fatal("failed to write messages:", err)
	}

	// 关闭连接
	if err := conn.Close(); err != nil {
		log.Fatal("failed to close writer:", err)
	}
}

```


## 消费消息

```go
// readByConn 连接至kafka后接收消息
func readByConn() {
	// 指定要连接的topic和partition
	topic := "my-topic"
	partition := 0

	// 连接至Kafka的leader节点
	conn, err := kafka.DialLeader(context.Background(), "tcp", "localhost:9092", topic, partition)
	if err != nil {
		log.Fatal("failed to dial leader:", err)
	}

	// 设置读取超时时间
	conn.SetReadDeadline(time.Now().Add(10 * time.Second))
	// 读取一批消息，得到的batch是一系列消息的迭代器
	batch := conn.ReadBatch(10e3, 1e6) // fetch 10KB min, 1MB max

	// 遍历读取消息
	b := make([]byte, 10e3) // 10KB max per message
	for {
		n, err := batch.Read(b)
		if err != nil {
			break
		}
		fmt.Println(string(b[:n]))
	}

	// 关闭batch
	if err := batch.Close(); err != nil {
		log.Fatal("failed to close batch:", err)
	}

	// 关闭连接
	if err := conn.Close(); err != nil {
		log.Fatal("failed to close connection:", err)
	}
}

```


使用`batch.Read`更高效一些，但是需要根据消息长度选择合适的buffer（上述代码中的b），如果传入的buffer太小（消息装不下）就会返回`io.ErrShortBuffer`错误。

如果不考虑内存分配的效率问题，也可以按以下代码使用`batch.ReadMessage`读取消息。

```go
for {
  msg, err := batch.ReadMessage()
  if err != nil {
    break
  }
  fmt.Println(string(msg.Value))
}

```

## 创建topic
当Kafka关闭自动创建topic的设置时，可按如下方式创建topic。

```go
// createTopicByConn 创建topic
func createTopicByConn() {
	// 指定要创建的topic名称
	topic := "my-topic"

	// 连接至任意kafka节点
	conn, err := kafka.Dial("tcp", "localhost:9092")
	if err != nil {
		panic(err.Error())
	}
	defer conn.Close()

	// 获取当前控制节点信息
	controller, err := conn.Controller()
	if err != nil {
		panic(err.Error())
	}
	var controllerConn *kafka.Conn
	// 连接至leader节点
	controllerConn, err = kafka.Dial("tcp", net.JoinHostPort(controller.Host, strconv.Itoa(controller.Port)))
	if err != nil {
		panic(err.Error())
	}
	defer controllerConn.Close()

	topicConfigs := []kafka.TopicConfig{
		{
			Topic:             topic,
			NumPartitions:     1,
			ReplicationFactor: 1,
		},
	}

	// 创建topic
	err = controllerConn.CreateTopics(topicConfigs...)
	if err != nil {
		panic(err.Error())
	}
}

```


## 通过非leader节点连接leader节点
下面的示例代码演示了如何通过已有的非leader节点的Conn，连接至 leader节点。

```go
conn, err := kafka.Dial("tcp", "localhost:9092")
if err != nil {
    panic(err.Error())
}
defer conn.Close()
// 获取当前控制节点信息
controller, err := conn.Controller()
if err != nil {
    panic(err.Error())
}
var connLeader *kafka.Conn
connLeader, err = kafka.Dial("tcp", net.JoinHostPort(controller.Host, strconv.Itoa(controller.Port)))
if err != nil {
    panic(err.Error())
}
defer connLeader.Close()

```

## 获取topic列表

```go
conn, err := kafka.Dial("tcp", "localhost:9092")
if err != nil {
    panic(err.Error())
}
defer conn.Close()

partitions, err := conn.ReadPartitions()
if err != nil {
    panic(err.Error())
}

m := map[string]struct{}{}
// 遍历所有分区取topic
for _, p := range partitions {
    m[p.Topic] = struct{}{}
}
for k := range m {
    fmt.Println(k)
}
```