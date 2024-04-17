`Reader`是由` kafka-go` 包提供的另一个概念，对于从单个主题-分区（topic-partition）消费消息这种典型场景，使用它能够简化代码。Reader 还实现了自动重连和偏移量管理，并支持使用 Context 支持异步取消和超时的 API。

?> 注意： 当进程退出时，必须在 `Reader` 上调用 `Close()` 。Kafka服务器需要一个优雅的断开连接来阻止它继续尝试向已连接的客户端发送消息。如果进程使用 SIGINT (shell 中的 Ctrl-C)或 SIGTERM (如 docker stop 或 kubernetes start)终止，那么下面给出的示例不会调用 `Close()`。当同一topic上有新Reader连接时，可能导致延迟(例如，新进程启动或新容器运行)。在这种场景下应使用`signal.Notify`处理程序在进程关闭时关闭Reader。

## 消费消息

下面的代码演示了如何使用Reader连接至Kafka消费消息。

```go
// readByReader 通过Reader接收消息
func readByReader() {
	// 创建Reader
	r := kafka.NewReader(kafka.ReaderConfig{
		Brokers:   []string{"localhost:9092", "localhost:9093", "localhost:9094"},
		Topic:     "topic-A",
		Partition: 0,
		MaxBytes:  10e6, // 10MB
	})
	r.SetOffset(42) // 设置Offset

	// 接收消息
	for {
		m, err := r.ReadMessage(context.Background())
		if err != nil {
			break
		}
		fmt.Printf("message at offset %d: %s = %s\n", m.Offset, string(m.Key), string(m.Value))
	}

	// 程序退出前关闭Reader
	if err := r.Close(); err != nil {
		log.Fatal("failed to close reader:", err)
	}
}

```


## 消费者组

`kafka-go`支持消费者组，包括broker管理的offset。要启用消费者组，只需在 `ReaderConfig` 中指定 `GroupID`。

使用消费者组时，ReadMessage 会自动提交偏移量。

```go
// 创建一个reader，指定GroupID，从 topic-A 消费消息
r := kafka.NewReader(kafka.ReaderConfig{
	Brokers:  []string{"localhost:9092", "localhost:9093", "localhost:9094"},
	GroupID:  "consumer-group-id", // 指定消费者组id
	Topic:    "topic-A",
	MaxBytes: 10e6, // 10MB
})

// 接收消息
for {
	m, err := r.ReadMessage(context.Background())
	if err != nil {
		break
	}
	fmt.Printf("message at topic/partition/offset %v/%v/%v: %s = %s\n", m.Topic, m.Partition, m.Offset, string(m.Key), string(m.Value))
}

// 程序退出前关闭Reader
if err := r.Close(); err != nil {
	log.Fatal("failed to close reader:", err)
}

```


在使用消费者组时会有以下限制：

- (*Reader).SetOffset 当设置了GroupID时会返回错误
- (*Reader).Offset 当设置了GroupID时会永远返回 -1
- (*Reader).Lag 当设置了GroupID时会永远返回 -1
- (*Reader).ReadLag 当设置了GroupID时会返回错误
- (*Reader).Stats 当设置了GroupID时会返回一个-1的分区


## 显式提交
`kafka-go` 也支持显式提交。当需要显式提交时不要调用 `ReadMessage`，而是调用 `FetchMessage`获取消息，然后调用 `CommitMessages` 显式提交。

```go
ctx := context.Background()
for {
    // 获取消息
    m, err := r.FetchMessage(ctx)
    if err != nil {
        break
    }
    // 处理消息
    fmt.Printf("message at topic/partition/offset %v/%v/%v: %s = %s\n", m.Topic, m.Partition, m.Offset, string(m.Key), string(m.Value))
    // 显式提交
    if err := r.CommitMessages(ctx, m); err != nil {
        log.Fatal("failed to commit messages:", err)
    }
}

```

在消费者组中提交消息时，具有给定主题/分区的最大偏移量的消息确定该分区的提交偏移量的值。例如，如果通过调用 `FetchMessage` 获取了单个分区的偏移量为 1、2 和 3 的消息，则使用偏移量为3的消息调用 `CommitMessages` 也将导致该分区的偏移量为 1 和 2 的消息被提交。

## 管理提交间隔
默认情况下，调用`CommitMessages`将同步向Kafka提交偏移量。为了提高性能，可以在ReaderConfig中设置CommitInterval来定期向Kafka提交偏移。

```go
// 创建一个reader从 topic-A 消费消息
r := kafka.NewReader(kafka.ReaderConfig{
    Brokers:        []string{"localhost:9092", "localhost:9093", "localhost:9094"},
    GroupID:        "consumer-group-id",
    Topic:          "topic-A",
    MaxBytes:       10e6, // 10MB
    CommitInterval: time.Second, // 每秒刷新一次提交给 Kafka
})

```