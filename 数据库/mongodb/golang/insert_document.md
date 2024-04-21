本章介绍Golang MongoDB的文档写入操作。

## 前置教程
<a href='/#/编程语言/golang/MongoDB/fast_induction.md'>Golang MongoDB快速入门</a>

## 插入一行文档
```go
result, err := collection.InsertOne(
    context.Background(),  // 上下文参数
    bson.D{   // 使用bson.D定义一个JSON文档
        {"item", "canvas"},
        {"qty", 100},
        {"tags", bson.A{"cotton"}},
        {"size", bson.D{
            {"h", 28},
            {"w", 35.5},
            {"uom", "cm"},
        }},
    })
```

// 获取文档生成的唯一id
```go
id := result.InsertedID
```

?>提示：不了解Golang MongoDB数据表达方式，请阅读Golang MongoDB数据模型章节。

相当于插入下面一个Json文档数据:
```json
{
    "item": "canvas",
    "qty": 100,
    "tags": ["cotton"],
    "size": {
        "h": 28,
        "w": 35.5,
        "uom": "cm"
    }
}
```

## 查询刚插入的文档
```go
cursor, err := collection.Find(
    context.Background(),
    bson.D{{"item", "canvas"}},  // 等价表达式: {"item": "canvas"}
)
```

## 批量插入
```go
result, err := collection.InsertMany(
    context.Background(),
    []interface{}{   // 传入一个文档数组， 插入三个文档数据
        bson.D{ // 文档数据1
            {"item", "journal"},
            {"qty", int32(25)},
            {"tags", bson.A{"blank", "red"}},
            {"size", bson.D{
                {"h", 14},
                {"w", 21},
                {"uom", "cm"},
            }},
        },
        bson.D{ // 文档数据2
            {"item", "mat"},
            {"qty", int32(25)},
            {"tags", bson.A{"gray"}},
            {"size", bson.D{
                {"h", 27.9},
                {"w", 35.5},
                {"uom", "cm"},
            }},
        },
        bson.D{ // 文档数据3
            {"item", "mousepad"},
            {"qty", 25},
            {"tags", bson.A{"gel", "blue"}},
            {"size", bson.D{
                {"h", 19},
                {"w", 22.85},
                {"uom", "cm"},
            }},
        },
    })
```

## 查询全部文档
```go
cursor, err := collection.Find(
    context.Background(),
    bson.D{}, // 传入空的查询条件
)
```