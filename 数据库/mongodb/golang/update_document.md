本章介绍Golang MongoDB如何更新文档数据。

## MongoDB支持的更新函数
- Collection.UpdateOne - 更新一条数据
- Collection.UpdateMany - 批量更新数据
- Collection.ReplaceOne - 替换数据

## 准备测试数据

往coll集合插入一批数据
```go
docs := []interface{}{
    bson.D{
        {"item", "canvas"},
        {"qty", 100},
        {"size", bson.D{
            {"h", 28},
            {"w", 35.5},
            {"uom", "cm"},
        }},
        {"status", "A"},
    },
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
        {"item", "mat"},
        {"qty", 85},
        {"size", bson.D{
            {"h", 27.9},
            {"w", 35.5},
            {"uom", "cm"},
        }},
        {"status", "A"},
    },
    bson.D{
        {"item", "mousepad"},
        {"qty", 25},
        {"size", bson.D{
            {"h", 19},
            {"w", 22.85},
            {"uom", "in"},
        }},
        {"status", "P"},
    },
    bson.D{
        {"item", "notebook"},
        {"qty", 50},
        {"size", bson.D{
            {"h", 8.5},
            {"w", 11},
            {"uom", "in"},
        }},
        {"status", "P"},
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
    bson.D{
        {"item", "sketchbook"},
        {"qty", 80},
        {"size", bson.D{
            {"h", 14},
            {"w", 21},
            {"uom", "cm"},
        }},
        {"status", "A"},
    },
    bson.D{
        {"item", "sketch pad"},
        {"qty", 95},
        {"size", bson.D{
            {"h", 22.85},
            {"w", 30.5},
            {"uom", "cm"},
        }},
        {"status", "A"},
    },
}

result, err := coll.InsertMany(context.Background(), docs)
```

##  更新一个文档
```go
result, err := coll.UpdateOne(
    context.Background(), // 获取上下文参数
    bson.D{ // 设置查询条件， 查询item=paper的文档
        {"item", "paper"},
    },
    bson.D{ // 设置更新表达式
        {"$set", bson.D{ // 使用$set操作符设置更新的字段内容
            {"size.uom", "cm"},  // 将size.uom的值改为cm
            {"status", "P"}, // 将status的值改为P
        }},
        {"$currentDate", bson.D{  //使用$currentDate操作符将lastModified字段值更新为最新的时间
            {"lastModified", true},
        }},
    },
)
```
根据查询条件匹配一个文档，然后更新这个文档内容。

?>提示：不了解MongoDB，请先阅读MongoDB教程

## 批量更新文档

跟UpdateOne函数的区别是，UpdateMany跟匹配条件，批量更新匹配的文档内容。
```go
result, err := coll.UpdateMany(
    context.Background(),
    bson.D{ // 设置查询条件, qty > 50
        {"qty", bson.D{
            {"$lt", 50},
        }},
    },
    bson.D{ // 设置更新内容
        {"$set", bson.D{
            {"size.uom", "cm"},
            {"status", "P"},
        }},
        {"$currentDate", bson.D{
            {"lastModified", true},
        }},
    },
)
```

## 替换一个文档
根据查询条件查询指定文档，并且将文档内容替换为指定内容。

?>提示：替换文档指的是整体替换，不是局部更新文档的某些字段值。

```go
result, err := coll.ReplaceOne(
    context.Background(),
    bson.D{ // 设置查询条件, item=paper
        {"item", "paper"},
    },
    bson.D{ // 设置新的文档内容
        {"item", "paper"},
        {"instock", bson.A{
            bson.D{
                {"warehouse", "A"},
                {"qty", 60},
            },
            bson.D{
                {"warehouse", "B"},
                {"qty", 40},
            },
        }},
    },
)
```
首先根据查询条件，找到item=paper的文档，然后替换成新的文档内容。