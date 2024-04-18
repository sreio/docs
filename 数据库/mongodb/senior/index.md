本章介绍MongoDB索引，类似MYSQL，MongoDB也支持索引，区别是MongoDB支持对JSON结构的任意嵌套字段添加索引，添加索引的目的都一样为了提高查询效率。

## MongoDB索引类型
### 单字段索引

类似MYSQL针对单个字段创建索引

格式:
```terminal
    db.collection.createIndex( { 字段名: 排序方向(1或者-1) } )
```

?>提示：排序方向 1 代表正序，-1 代表逆序。

例子1：给score字段创建索引。
```terminal
    db.records.createIndex( { score: 1 } )
```

例子2：给location.state嵌套JSON字段创建索引
```terminal
    db.records.createIndex( { "location.state": 1 } )
```

### 组合索引

类似MYSQL可以使用多个字段组合起来一起创建索引，加快复合查询的速度。

格式:
```terminal
    db.collection.createIndex( { <field1>: <type>, <field2>: <type2>, ... } )
```

例子：为item和stock两个字段创建一个组合索引，都是正序（1代表正序）。
```terminal
    db.products.createIndex( { "item": 1, "stock": 1 } )
```

### 文本索引（全文索引）

文本索引（text）,主要用于支持全文索引。

?>提示：一个集合只允许一个text索引，但是一个text索引可以包括多个字段。

例子：为name和description字段创建text全文索引。
```terminal
db.stores.createIndex( { name: "text", description: "text" } )
```

?>提示：详请参考全文索引章节。

### 空间索引（GEO索引）
主要用于支持地理信息相关查询，主要支持2dsphere和2d两种索引类型。<a href='/#/数据库/mongodb/position/model.md'>详情请参考地理信息查询章节</a>

## 删除索引

查询集合中的所有索引
```terminal
    db.pets.getIndexes()
```

删除pets集合中catIdx索引
```terminal
    db.pets.dropIndex( "catIdx" )
```