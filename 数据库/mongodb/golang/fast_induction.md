本教程从Golang语言角度介绍MongoDB的基本操作。

?>提示：本教程使用MongoDB的官方提供的Go语言驱动包操作MongoDB。

## 前置教程
<a href='/#/数据库/mongodb/README'>MongoDB教程</a>

?>提示：不熟悉MongoDB，请先学习下MongoDB知识，Golang教程不会重复介绍相关知识。

## 基本要求
- Go 1.10以上
- MongoDB 2.6以上

## 安装依赖
```terminal
    go get go.mongodb.org/mongo-driver/mongo
```

## 连接MongoDB
```go
import (
    "go.mongodb.org/mongo-driver/mongo"
    "go.mongodb.org/mongo-driver/mongo/options"
    "go.mongodb.org/mongo-driver/mongo/readpref"
)

// MongoDB连接地址
uri := "mongodb://localhost:27017"
// 获取上下文对象，可以设置超时时间
ctx, cancel := context.WithTimeout(context.Background(), 10*time.Second)
defer cancel()

// 连接MongoDB
client, err := mongo.Connect(ctx, options.Client().ApplyURI(uri))
```

Connect函数不会阻塞协程，也就是说调用Connect方法之后，直接操作MongoDB可能会因为MongoDB还没连接成功而报错，可以使用Ping方法检测下MongoDB是否连接成功。
```go
// PIng下MongoDB，如果连接成功不会返回错误
err = client.Ping(ctx, readpref.Primary())
```

## MongoDB连接地址

MongoDB连接地址，包括服务器地址、端口号、账号、密码等关键信息。

格式如下：
```terminal
    mongodb://[username:password@]host1[:port1][,...hostN[:portN]][/[defaultauthdb][?options]]
```

例子:
```terminal
// 使用默认账号密码，连接localhost:27017地址
mongodb://localhost:27017

// 连接localhost:27017地址，账号=root， 密码=123456，连接默认数据库=admin
mongodb://root:123456@localhost:27017/admin
```

## 释放连接资源

如果不使用连接了，需要手动释放连接资源
```go
defer func() {
    if err = client.Disconnect(ctx); err != nil {
        panic(err)
    }
}()
```

## 获取集合实例

golang操作MongoDB首先得拿到集合实例，才可以对集合实例进行增删改查。
```go
// 通过Database设置数据库名，通过Collection设置集合名字
collection := client.Database("testing").Collection("numbers")
```

?>提示：根据MongoDB的特性，所操作数据库和集合，并不要求提前创建好，在首次写入数据的时候会自动创建。

## 插入文档

通过InsertOne函数插入数据。
```go
// 上下文对象，常用用于设置请求超时时间，也可以共用前面创建的上下文对象，不必重新定义。
ctx, cancel = context.WithTimeout(context.Background(), 5*time.Second)
defer cancel()

// 插入文档，这里使用bson.D类型描述JSON文档，bson.D代表一个json数组
res, err := collection.InsertOne(ctx, bson.D{{"name", "pi"}, {"value", 3.14159}})

// 获取新增文档的id
id := res.InsertedID
```

需要导入下面包
```go
import (
    "go.mongodb.org/mongo-driver/bson"
    "go.mongodb.org/mongo-driver/mongo"
    "go.mongodb.org/mongo-driver/mongo/options"
    "go.mongodb.org/mongo-driver/mongo/readpref"
)
```
?>提示：Golang 使用bson包中定义的数据结构表示JSON文档，后面的章节会详细介绍Golang的MongoDB数据存储结构的表达方式。

## 完整的例子
```go
package main
import (
    "context"
    "fmt"
    "time"
    "go.mongodb.org/mongo-driver/bson"
    "go.mongodb.org/mongo-driver/mongo"
    "go.mongodb.org/mongo-driver/mongo/options"
    "go.mongodb.org/mongo-driver/mongo/readpref"
)

func main() {
    // 设置MongoDB连接地址
    uri := "mongodb://root:123456@localhost:27017/admin"
    ctx, cancel := context.WithTimeout(context.Background(), 10*time.Second)
    defer cancel()
    // 连接MongoDB
    client, err := mongo.Connect(ctx, options.Client().ApplyURI(uri))
    if err != nil {
        panic(err)
    }

    defer func() {
        // 延迟释放连接
        if err = client.Disconnect(ctx); err != nil {
            panic(err)
        }
    }()

    // 检测MongoDB是否连接成功
    if err := client.Ping(ctx, readpref.Primary()); err != nil {
        panic(err)
    }
    fmt.Println("MongoDB连接成功！")

    // 获取numbers集合
    collection := client.Database("testing").Collection("numbers")

    // 插入一个文档
    res, err := collection.InsertOne(ctx, bson.D{{"name", "pi"}, {"value", 3.14159}})
    id := res.InsertedID
    fmt.Println("新增文档Id=", id)
}
```