## 1. 安装依赖
```terminal
go get go.etcd.io/etcd/clientv3
```

## 2.go操作etcd步骤

- 通过clientv3.New创建etcd客户端，连接etcd。
- 通过步骤1创建的etcd客户端操作etcd。
- 关闭etcd连接。

## 3.连接etcd
通过clientv3.New创建一个etcd客户端

```go
// 通过clientv3.Config配置，客户端参数
cli, err := clientv3.New(clientv3.Config{
        // etcd服务端地址数组，可以配置一个或者多个
	Endpoints:   []string{"localhost:2379", "localhost:22379", "localhost:32379"},
        // 连接超时时间，5秒
	DialTimeout: 5 * time.Second,
})

if err != nil {
	// 错误处理
}

// ...对etcd进行crud操作....

// 延迟关闭客户端，记得用完后关闭客户端
defer cli.Close()
```

---

?> etcd是一个键值存储系统，类似ZooKeeper, key是以目录结构形式组织的，如下：

key的命名例子:

```
/sreio
/sreio/site/name
/sreio/site/domain
/sreio/status/urls
```
key以这种目录树结构方式存储，etcd支持前缀搜索，例如：搜索key 以 /sreio 为前缀的所有键值。

下面介绍golang对etcd的基本操作。

## 4.写入数据
通过Put函数写入数据，如果Key存在则覆盖，否则新建一个。

```go
cli, err := clientv3.New(...省略...)
if err != nil {
    log.Fatal(err)
}
defer cli.Close()

// 获取上下文，设置请求超时时间为5秒
ctx, _ := context.WithTimeout(context.Background(), 5 * time.Second)
// 设置key="/sreio/url" 的值为 www.sreio.com
_, err = cli.Put(ctx, "/sreio/url", "www.sreio.com")

if err != nil {
    log.Fatal(err)
}
```

## 5.查询数据
通过Get函数，可以查询key的值

```go
cli, err := clientv3.New(...省略...)
if err != nil {
    log.Fatal(err)
}
defer cli.Close()

// 获取上下文，设置请求超时时间为5秒
ctx, _ := context.WithTimeout(context.Background(), 5 * time.Second)
// 读取key="/sreio/url" 的值
resp, err := cli.Get(ctx, "/sreio/url")

if err != nil {
    log.Fatal(err)
}

// 虽然这个例子我们只是查询一个Key的值，
// 但是Get的查询结果可以表示多个Key的结果例如我们根据Key进行前缀匹配,Get函数可能会返回多个值。
for _, ev := range resp.Kvs {
    fmt.Printf("%s : %s\n", ev.Key, ev.Value)
}
```


## 6.前缀匹配
etcd支持key前缀匹配，Get,Delele函数都支持前缀匹配，只需要添加clientv3.WithPrefix()参数即可。

例子:
```go
cli, err := clientv3.New(...省略...)
if err != nil {
    log.Fatal(err)
}
defer cli.Close()

// 获取上下文，设置请求超时时间为5秒
ctx, _ := context.WithTimeout(context.Background(), 5 * time.Second)

// 读取key前缀等于"/sreio/"的所有值
resp, err := cli.Get(ctx, "/sreio/", clientv3.WithPrefix())

if err != nil {
    log.Fatal(err)
}

// 遍历查询结果
for _, ev := range resp.Kvs {
    fmt.Printf("%s : %s\n", ev.Key, ev.Value)
}
```

## 7.删除数据
通过Delete函数删除数据
```go
cli, err := clientv3.New(...省略...)
if err != nil {
    log.Fatal(err)
}
defer cli.Close()

// 获取上下文，设置请求超时时间为5秒
ctx, _ := context.WithTimeout(context.Background(), 5 * time.Second)
// 删除key="/sreio/url" 的值
_, err = cli. Delete(ctx, "/sreio/url")

if err != nil {
    log.Fatal(err)
}

// 批量删除key以"/sreio/"为前缀的值
// 加上clientv3.WithPrefix()参数代表key前缀匹配的意思
_, err = cli. Delete(ctx, "/sreio/", clientv3.WithPrefix())
if err != nil {
    log.Fatal(err)
}
```

---

?> etcd的核心特性之一，就是我们可以监控key的数据变化，只要有人修改了key的值，我们都可以监控到变化的值。

## 8.监控指定Key
```go
cli, err := clientv3.New(...忽略...)
if err != nil {
    log.Fatal(err)
}
defer cli.Close()

// 监控key=/tizi 的值
rch := cli.Watch(context.Background(), "/tizi")
// 通过channel遍历key的值的变化
for wresp := range rch {
    for _, ev := range wresp.Events {
        fmt.Printf("%s %q : %q\n", ev.Type, ev.Kv.Key, ev.Kv.Value)
    }
}
```

## 9.根据key前缀监控一组key的值
```go
cli, err := clientv3.New(...忽略...)
if err != nil {
    log.Fatal(err)
}
defer cli.Close()

// 监控以/tizi为前缀的所有key的值
rch := cli.Watch(context.Background(), "/tizi", clientv3.WithPrefix())
// 通过channel遍历key的值的变化
for wresp := range rch {
    for _, ev := range wresp.Events {
        fmt.Printf("%s %q : %q\n", ev.Type, ev.Kv.Key, ev.Kv.Value)
    }
}
```
