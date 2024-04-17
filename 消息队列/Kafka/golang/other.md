## TLS
对于基本的 Conn 类型或在 Reader/Writer 配置中，可以在Dialer中设置TLS选项。如果 TLS 字段为空，则它将不启用TLS 连接。

?> 注意：不在Conn/Reder/Writer上配置TLS，连接到启用TLS的Kafka集群，可能会出现`io.ErrUnexpectedEOF`错误。

### Connection
```go
dialer := &kafka.Dialer{
    Timeout:   10 * time.Second,
    DualStack: true,
    TLS:       &tls.Config{...tls config...},  // 指定TLS配置
}

conn, err := dialer.DialContext(ctx, "tcp", "localhost:9093")
```

### Reader
```go
dialer := &kafka.Dialer{
    Timeout:   10 * time.Second,
    DualStack: true,
    TLS:       &tls.Config{...tls config...},  // 指定TLS配置
}

r := kafka.NewReader(kafka.ReaderConfig{
    Brokers:        []string{"localhost:9092", "localhost:9093", "localhost:9094"},
    GroupID:        "consumer-group-id",
    Topic:          "topic-A",
    Dialer:         dialer,
})
```
### Writer

创建`Writer`时可以按如下方式指定TLS配置。
```go
w := kafka.Writer{
    Addr: kafka.TCP("localhost:9092", "localhost:9093", "localhost:9094"), 
    Topic:   "topic-A",
    Balancer: &kafka.Hash{},
    Transport: &kafka.Transport{
        TLS: &tls.Config{},  // 指定TLS配置
      },
    }
```


## SASL

可以在`Dialer`上指定一个选项以使用SASL身份验证。`Dialer`可以直接用来打开一个 Conn，也可以通过它们各自的配置传递给一个 `Reader` 或 `Writer`。如果` SASLMechanism`字段为 nil，则不会使用 SASL 进行身份验证。

### SASL 身份验证类型

#### 明文
```go
mechanism := plain.Mechanism{
    Username: "username",
    Password: "password",
}
```

#### SCRAM
```go
mechanism, err := scram.Mechanism(scram.SHA512, "username", "password")
if err != nil {
    panic(err)
}
```

### Connection
```go
mechanism, err := scram.Mechanism(scram.SHA512, "username", "password")
if err != nil {
    panic(err)
}

dialer := &kafka.Dialer{
    Timeout:       10 * time.Second,
    DualStack:     true,
    SASLMechanism: mechanism,
}

conn, err := dialer.DialContext(ctx, "tcp", "localhost:9093")
```

#### Reader
```go
mechanism, err := scram.Mechanism(scram.SHA512, "username", "password")
if err != nil {
    panic(err)
}

dialer := &kafka.Dialer{
    Timeout:       10 * time.Second,
    DualStack:     true,
    SASLMechanism: mechanism,
}

r := kafka.NewReader(kafka.ReaderConfig{
    Brokers:        []string{"localhost:9092","localhost:9093", "localhost:9094"},
    GroupID:        "consumer-group-id",
    Topic:          "topic-A",
    Dialer:         dialer,
})
```

#### Writer
```go
mechanism, err := scram.Mechanism(scram.SHA512, "username", "password")
if err != nil {
    panic(err)
}

// Transport 负责管理连接池和其他资源,
// 通常最好的使用方式是创建后在应用程序中共享使用它们。
sharedTransport := &kafka.Transport{
    SASL: mechanism,
}

w := kafka.Writer{
	Addr:      kafka.TCP("localhost:9092", "localhost:9093", "localhost:9094"),
	Topic:     "topic-A",
	Balancer:  &kafka.Hash{},
	Transport: sharedTransport,
}
```

#### Client
```go
mechanism, err := scram.Mechanism(scram.SHA512, "username", "password")
if err != nil {
    panic(err)
}

// Transport 负责管理连接池和其他资源,
// 通常最好的使用方式是创建后在应用程序中共享使用它们。
sharedTransport := &kafka.Transport{
    SASL: mechanism,
}

client := &kafka.Client{
    Addr:      kafka.TCP("localhost:9092", "localhost:9093", "localhost:9094"),
    Timeout:   10 * time.Second,
    Transport: sharedTransport,
}
```


## Balancer
`kafka-go`实现了多种负载均衡策略。特别是当你从其他Kafka库迁移过来时，你可以按如下说明选择合适的Balancer实现。

### Sarama

如果从 sarama 切换过来，并且需要/希望使用相同的算法进行消息分区，则可以使用kafka.Hash或kafka.ReferenceHash。

- kafka.Hash = sarama.NewHashPartitioner
- kafka.ReferenceHash = sarama.NewReferenceHashPartitioner

```go
w := &kafka.Writer{
	Addr:     kafka.TCP("localhost:9092", "localhost:9093", "localhost:9094"),
	Topic:    "topic-A",
	Balancer: &kafka.Hash{},
}

```

#### librdkafka和confluent-kafka-go

`kafka.CRC32Balancer`与l`ibrdkafka`默认的`consistent_random`策略表现一致。

```go
w := &kafka.Writer{
	Addr:     kafka.TCP("localhost:9092", "localhost:9093", "localhost:9094"),
	Topic:    "topic-A",
	Balancer: kafka.CRC32Balancer{},
}
```

#### Java

使用`kafka.Murmur2Balancer`可以获得与默认Java客户端相同的策略。

```go
w := &kafka.Writer{
	Addr:     kafka.TCP("localhost:9092", "localhost:9093", "localhost:9094"),
	Topic:    "topic-A",
	Balancer: kafka.Murmur2Balancer{},
}
```

#### Compression
可以通过设置`Compression`字段在Writer上启用压缩：

```go
w := &kafka.Writer{
	Addr:        kafka.TCP("localhost:9092", "localhost:9093", "localhost:9094"),
	Topic:       "topic-A",
	Compression: kafka.Snappy,
}
```
`Reader` 将通过检查消息属性来确定消费的消息是否被压缩。

## Logging

想要记录Reader/Writer类型的操作，可以在创建时配置日志记录器。

kafka-go中的`Logger`是一个接口类型。

```go
type Logger interface {
	Printf(string, ...interface{})
}
```

并且提供了一个`LoggerFunc`类型，帮我们实现了`Logger`接口。

```go
type LoggerFunc func(string, ...interface{})

func (f LoggerFunc) Printf(msg string, args ...interface{}) { f(msg, args...) }
```

### Reader
借助`kafka.LoggerFunc`我们可以自定义一个`Logger`。

```go
// 自定义一个Logger
func logf(msg string, a ...interface{}) {
	fmt.Printf(msg, a...)
	fmt.Println()
}

r := kafka.NewReader(kafka.ReaderConfig{
	Brokers:     []string{"localhost:9092", "localhost:9093", "localhost:9094"},
	Topic:       "q1mi-topic",
	Partition:   0,
	Logger:      kafka.LoggerFunc(logf),
	ErrorLogger: kafka.LoggerFunc(logf),
})
```

### Writer

也可以直接使用第三方日志库，例如下面示例代码中使用了`zap`日志库。

```go
w := &kafka.Writer{
	Addr:        kafka.TCP("localhost:9092"),
	Topic:       "q1mi-topic",
	Logger:      kafka.LoggerFunc(zap.NewExample().Sugar().Infof),
	ErrorLogger: kafka.LoggerFunc(zap.NewExample().Sugar().Errorf),
}
```