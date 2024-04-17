## 安装

```bash
go get github.com/segmentio/kafka-go
```

?> 注意：kafka-go 需要 Go 1.15或更高版本。

## 使用指南

`kafka-go` 提供了两套与Kafka交互的API。

- 低级别（ low-level）：基于与 Kafka 服务器的原始网络连接实现。
- 高级别（high-level）：对于常用读写操作封装了一套更易用的API。

通常建议直接使用高级别的交互API。


