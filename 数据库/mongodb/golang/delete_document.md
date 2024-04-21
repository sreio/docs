本章介绍Golang MongoDB文档删除操作。

## 支持的删除操作
```go
Collection.DeleteOne  //根据条件删除一个文档
Collection.DeleteMany  // 根据条件匹配删除文档
```

## 删除一个文档
```go
res, err := coll.DeleteOne(
                        context.TODO(), // 上下文参数
                        bson.D{{"name", "bob"}} // 设置查询条件name=bob
                    )
if err != nil {
    log.Fatal(err)
}
// 打印删除的文档数量
fmt.Printf("成功删除 %v 文档数\n", res.DeletedCount)
```
根据查询条件删除一个文档

## 批量删除文档
```go
// 根据查询条件name=bob， 批量删除匹配的文档
res, err := coll.DeleteMany(context.TODO(), bson.D{{"name", "bob"}})
if err != nil {
        log.Fatal(err)
}
// 打印删除的文档数量
fmt.Printf("deleted %v documents\n", res.DeletedCount)
```