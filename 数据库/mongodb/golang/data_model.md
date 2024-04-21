本章介绍Golang如何表达MongoDB的JSON数据结构，我们都知道MongoDB的数据和查询条件是用JSON结构描述的，那么在Go语言中，怎么表达这种JSON结构？

## bson包
MongoDB的官方Go语言驱动，提供了一个bson包，里面提供了几个数据结构用于描述JSON数据。

bson包
```terminal
    go.mongodb.org/mongo-driver/bson
```

## 键值数组

bson.D类型，用于描述有序的键值数组，常用于表达MongoDB查询表达式和JSON数据。

定义:
```go
// 键值数组
type D [] E

// 键值结构
type E struct {
    Key   string
    Value interface{}
}
```

查询表达式例子：
```go
    bson.D{{"qty", bson.D{{"$lt", 30}}}}
```

等价表达式:
```terminal
    {"qty": {"$lt": 30} }
```

文档数据例子:
```go
bson.D{
        {"item", "journal"},
        {"qty", 25},
        {"size", bson.D{
            {"h", 14},
            {"w", 21},
            {"uom", "cm"},
        }},
        {"status", "A"},
    }
```

等价JSON：
```json
{
    "item": "journal",
    "qty": 25,
    "size": {
        "h": 14,
        "w": 21,
        "uom": "cm"
    },
    "status": "A"
}
```

## JSON数组
bson.A用于定义JSON数组。

JSON数组定义如下
```go
    type A []interface{}

```
例子1:
```go
    bson.A{"bar", "world", 3.14159, bson.D{{"qux", 12345}}}
```

等价JSON:
```json
    ["bar", "world", 3.14159, {"qux": 12345}]
```

例子2：
```go
    bson.A{"A", "D"}
```

等价JSON：
```json
    ["A", "D"]
```

## map键值结构
bson.M用于描述无序的键值对, 跟bson.D的区别是，是否在乎KEY的存储顺序

定义:
```go
    type M map[string]interface{}
```

例子：
```go
    bson.M{"foo": "bar", "hello": "world", "pi": 3.14159}
```

等价JSON：
```json
{
    "foo": "bar",
    "hello": "world",
    "pi": 3.14159
}
```