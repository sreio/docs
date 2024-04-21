本章介绍Golang MongoDB文档查询的用法。

## 前置教程
- <a href='/#/数据库/mongodb/README'>MongoDB教程</a>
- <a href='/#/编程语言/golang/MongoDB/fast_induction'>Golang MongoDB快速入门</a>

?>提示：不了解MongoDB查询语法，请先阅读MongoDB教程，Golang操作MongoDB使用的也是一样的表达式语法。

## 准备测试数据
往coll集合插入一批文档数据。

```go
docs := []interface{}{
    bson.D{
        {"item", "journal"},
        {"qty", 25},
        {"size", bson.D{
            {"h", 14},
            {"w", 21},
            {"uom", "cm"},
        }},
        {"status", "A"},
    },
    bson.D{
        {"item", "notebook"},
        {"qty", 50},
        {"size", bson.D{
            {"h", 8.5},
            {"w", 11},
            {"uom", "in"},
        }},
        {"status", "A"},
    },
    bson.D{
        {"item", "paper"},
        {"qty", 100},
        {"size", bson.D{
            {"h", 8.5},
            {"w", 11},
            {"uom", "in"},
        }},
        {"status", "D"},
    },
    bson.D{
        {"item", "planner"},
        {"qty", 75},
        {"size", bson.D{
            {"h", 22.85},
            {"w", 30},
            {"uom", "cm"},
        }},
        {"status", "D"},
    },
    bson.D{
        {"item", "postcard"},
        {"qty", 45},
        {"size", bson.D{
            {"h", 10},
            {"w", 15.25},
            {"uom", "cm"},
        }},
        {"status", "A"},
    },
}

result, err := coll.InsertMany(context.Background(), docs)
```

## 查询所有文档
```go
cursor, err := coll.Find(
    context.Background(),
    bson.D{}, // 设置空的查询条件
)
```

## 等值查询条件

类似SQL的等值匹配
```go
cursor, err := coll.Find(
    context.Background(),
    bson.D{{"status", "D"}},   // 等价条件：status = D
)
```

## in查询操作符

类似SQL的In查询
```go
cursor, err := coll.Find(
    context.Background(),
    bson.D{{"status", bson.D{{"$in", bson.A{"A", "D"}}}}}) // 等价条件： status in ('A', 'D')
```

## and查询条件

类似SQL的and逻辑表达式。
```go
cursor, err := coll.Find(
    context.Background(),
    bson.D{ // 等价条件: status='A' and qty < 30 
        {"status", "A"},
        {"qty", bson.D{{"$lt", 30}}},
    })
```

## or查询条件

类似SQL的or逻辑表达式
```go
cursor, err := coll.Find(
    context.Background(),
    bson.D{ // 等价条件：status = "A" OR qty < 30
        {"$or", // 使用$or操作符
            bson.A{ // 使用bson.A数组类型定义
                bson.D{{"status", "A"}},
                bson.D{{"qty", bson.D{{"$lt", 30}}}},
            }},
    })
```

## 复合查询例子
```go
cursor, err := coll.Find(
    context.Background(),
    bson.D{ // 等价条件: status = "A" AND ( qty < 30 OR item LIKE "p%")
        {"status", "A"},
        {"$or", bson.A{
            bson.D{{"qty", bson.D{{"$lt", 30}}}},
            bson.D{{"item", primitive.Regex{Pattern: "^p", Options: ""}}},
        }},
    })
```

## 遍历查询结果
### 例子1
```go
// 查询name=Bob的文档
cursor, err := coll.Find(context.TODO(), bson.D{{"name", "Bob"}}, opts)
if err != nil {
    log.Fatal(err)
}

// 定义bson.M类型的文档数组，bson.M是一个map类型的键值数据结构
var results []bson.M
// 使用All函数获取所有查询结果，并将结果保存至results变量。
if err = cursor.All(context.TODO(), &results); err != nil {
    log.Fatal(err)
}

// 遍历结果数组
for _, result := range results {
    fmt.Println(result)
}
```
说明：例子使用bson.M类型保存文档数据，你可以自定义一个struct类型，只要字段跟文档匹配上就行。

### 例子2：
```go
// 定义一个struct类型的变量，用于保存查询结果
var result struct {
    Value float64
}

// 定义文档查询条件 name = 'pi'
filter := bson.D{{"name", "pi"}}
// 创建上下文对象，设置查询超时时间为5秒
ctx, cancel = context.WithTimeout(context.Background(), 5*time.Second)
defer cancel()

// 根据条件查询一条数据，并使用Decode函数将结果保存至result变量
err = collection.FindOne(ctx, filter).Decode(&result)

// 检测查询错误
if err == mongo.ErrNoDocuments {
    // 找不到文档记录
    fmt.Println("文档不存在")
} else if err != nil {
    log.Fatal(err)
}
// 其他处理
```
?>提示：其他更丰富的MongoDB查询表达式语法，请参考MongoDB教程。