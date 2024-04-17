向Kafka发送消息，除了使用基于`Conn`的低级API，`kafka-go`包还提供了更高级别的 `Writer` 类型。大多数情况下使用`Writer`即可满足条件，它支持以下特性。

- 对错误进行自动重试和重新连接。
- 在可用分区之间可配置的消息分布。
- 向Kafka同步或异步写入消息。
- 使用Context的异步取消。
- 关闭时清除挂起的消息以支持正常关闭。
- 在发布消息之前自动创建不存在的topic。



## 发送消息


```go
// 创建一个writer 向topic-A发送消息
w := &kafka.Writer{
	Addr:         kafka.TCP("localhost:9092", "localhost:9093", "localhost:9094"),
	Topic:        "topic-A",
	Balancer:     &kafka.LeastBytes{}, // 指定分区的balancer模式为最小字节分布
	RequiredAcks: kafka.RequireAll,    // ack模式
	Async:        true,                // 异步
}

err := w.WriteMessages(context.Background(),
	kafka.Message{
		Key:   []byte("Key-A"),
		Value: []byte("Hello World!"),
	},
	kafka.Message{
		Key:   []byte("Key-B"),
		Value: []byte("One!"),
	},
	kafka.Message{
		Key:   []byte("Key-C"),
		Value: []byte("Two!"),
	},
)
if err != nil {
    log.Fatal("failed to write messages:", err)
}

if err := w.Close(); err != nil {
    log.Fatal("failed to close writer:", err)
}

```


## 创建不存在的topic

如果给Writer配置了`AllowAutoTopicCreation:true`，那么当发送消息至某个不存在的topic时，则会自动创建topic。

```go
w := &Writer{
    Addr:                   kafka.TCP("localhost:9092", "localhost:9093", "localhost:9094"),
    Topic:                  "topic-A",
    AllowAutoTopicCreation: true,  // 自动创建topic
}

messages := []kafka.Message{
    {
        Key:   []byte("Key-A"),
        Value: []byte("Hello World!"),
    },
    {
        Key:   []byte("Key-B"),
        Value: []byte("One!"),
    },
    {
        Key:   []byte("Key-C"),
        Value: []byte("Two!"),
    },
}

var err error
const retries = 3
// 重试3次
for i := 0; i < retries; i++ {
    ctx, cancel := context.WithTimeout(context.Background(), 10*time.Second)
    defer cancel()
    
    err = w.WriteMessages(ctx, messages...)
    if errors.Is(err, LeaderNotAvailable) || errors.Is(err, context.DeadlineExceeded) {
        time.Sleep(time.Millisecond * 250)
        continue
    }

    if err != nil {
        log.Fatalf("unexpected error %v", err)
    }
    break
}

// 关闭Writer
if err := w.Close(); err != nil {
    log.Fatal("failed to close writer:", err)
}

```

## 写入多个topic


```go
通常，`WriterConfig.Topic`用于初始化单个topic的Writer。通过去掉WriterConfig中的Topic配置，分别设置每条消息的`message.topic`，可以实现将消息发送至多个topic。
```


```go
w := &kafka.Writer{
	Addr:     kafka.TCP("localhost:9092", "localhost:9093", "localhost:9094"),
    // 注意: 当此处不设置Topic时,后续的每条消息都需要指定Topic
	Balancer: &kafka.LeastBytes{},
}

err := w.WriteMessages(context.Background(),
    // 注意: 每条消息都需要指定一个 Topic, 否则就会报错
	kafka.Message{
        Topic: "topic-A",
		Key:   []byte("Key-A"),
		Value: []byte("Hello World!"),
	},
	kafka.Message{
        Topic: "topic-B",
		Key:   []byte("Key-B"),
		Value: []byte("One!"),
	},
	kafka.Message{
        Topic: "topic-C",
		Key:   []byte("Key-C"),
		Value: []byte("Two!"),
	},
)
if err != nil {
    log.Fatal("failed to write messages:", err)
}

if err := w.Close(); err != nil {
    log.Fatal("failed to close writer:", err)
}

```


?> 注意：Writer中的Topic和Message中的Topic是互斥的，同一时刻有且只能设置一处。

