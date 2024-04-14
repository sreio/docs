> go-redis

- `多种客户端`: 支持单机Redis Server、Redis Cluster、Redis Sentinel、Redis分片服务器

- `数据类型`: go-redis会根据不同的redis命令处理成指定的数据类型，不必进行繁琐的数据类型转换

- `功能完善`: go-redis支持管道(pipeline)、事务、pub/sub、Lua脚本、mock、分布式锁等功能

- 官方文档地址： https://redis.uptrace.dev/zh/guide/go-redis.html
- GitHub地址：https://github.com/redis/go-redis


## 安装
```bash
go get github.com/redis/go-redis/v9
```

## 连接到 Redis 服务器

```go
import "github.com/redis/go-redis/v9"

rdb := redis.NewClient(&redis.Options{
	Addr:	  "localhost:6379",
	Password: "", // 没有密码，默认值
	DB:		  0,  // 默认DB 0
})
```
同时也支持另外一种常见的连接字符串:
```go
opt, err := redis.ParseURL("redis://<user>:<pass>@localhost:6379/<db>")
if err != nil {
	panic(err)
}

rdb := redis.NewClient(opt)
```

## Context 上下文

go-redis 支持 Context，你可以使用它控制 超时 或者传递一些数据, 也可以 监控 go-redis 性能。
```go
ctx := context.Background()
```

## 执行 Redis 命令

执行 Redis 命令:
```go
val, err := rdb.Get(ctx, "key").Result()
fmt.Println(val)
```
你也可以分别访问值和错误：
```go
get := rdb.Get(ctx, "key")
```

## 执行尚不支持的命令

可以使用 Do() 方法执行尚不支持或者任意命令:
```go
val, err := rdb.Do(ctx, "get", "key").Result()
if err != nil {
	if err == redis.Nil {
		fmt.Println("key does not exists")
		return
	}
	panic(err)
}
fmt.Println(val.(string))
```
Do() 方法返回 Cmd 类型，你可以使用它获取你想要的类型：
```go
// Text is a shortcut for get.Val().(string) with proper error handling.
val, err := rdb.Do(ctx, "get", "key").Text()
fmt.Println(val, err)
```

## Conn

redis.Conn 是从连接池中取出的单个连接，除非你有特殊的需要，否则尽量不要使用它。你可以使用它向 redis 发送任何数据并读取 redis 的响应，当你使用完毕时，应该把它返回给 go-redis，否则连接池会永远丢失一个连接。

```go
cn := rdb.Conn(ctx)
defer cn.Close()

if err := cn.ClientSetName(ctx, "myclient").Err(); err != nil {
	panic(err)
}

name, err := cn.ClientGetName(ctx).Result()
if err != nil {
	panic(err)
}
fmt.Println("client name", name)
```

## 连接池大小

go-redis 底层维护了一个连接池，不需要手动管理。默认情况下， go-redis 连接池大小为 `runtime.GOMAXPROCS * 10`，在大多数情况下默认值已经足够使用，且设置太大的连接池几乎没有什么用，可以在 配置项 中调整连接池数量：

```go
rdb := redis.NewClient(&redis.Options{
    PoolSize: 1000,
})
```
这里介绍一下连接池的配置（连接池配置全部继承自 配置项 ）：

```go
type Options struct {
	// 创建网络连接时调用的函数
    Dialer  func(context.Context) (net.Conn, error)

	// 连接池模式，FIFO和LIFO模式
    PoolFIFO        bool

	// 连接池大小
    PoolSize        int

	// 从连接池获取连接超时时间（如果所有连接都繁忙，等待的时间）
    PoolTimeout     time.Duration

	// 最小空闲连接数，受PoolSize限制
    MinIdleConns    int

	// 最大空闲连接数，多余会被关闭
    MaxIdleConns    int

	// 每个连接最大空闲时间，如果超过了这个时间会被关闭
    ConnMaxIdleTime time.Duration

	// 连接最大生命周期
    ConnMaxLifetime time.Duration
}
```

连接池的结构：
```go
type ConnPool struct {
	// 配置项信息
	cfg *Options

	// 创建网络连接错误次数
	// 如果超过了 `Options.PoolSize` 次，再创建新连接时，会直接返回 `lastDialError` 错误，
	// 同时会进行间隔1秒的探活操作，如果探活成功，会把 `dialErrorsNum` 重新设置为0
	dialErrorsNum uint32 // atomic

	// 用于记录最后一次创建新连接的错误信息
	lastDialError atomic.Value

	// 长度为 `Options.PoolSize` 的chan
	// 从连接池获取连接时向chan放入一个数据，返还连接时从chan中取出一个数据
	// 如果chan已满，则证明连接池所有连接已经都被使用，需要等待
	queue chan struct{}

	// 连接池并发安全锁
	connsMu   sync.Mutex

	// 所有连接列表
	conns     []*Conn

	// 空闲连接
	idleConns []*Conn

	// 当前连接池大小，最大为 `Options.PoolSize`
	poolSize     int

	// 空闲连接长度
	idleConnsLen int

	// 统计状态的
	stats Stats

	// 连接池是否被关闭的 atomic 值，1-被关闭
	_closed  uint32 // atomic
}
```