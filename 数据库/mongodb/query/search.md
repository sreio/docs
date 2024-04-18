本章节，介绍MongoDB的全文搜索，区别于SQL的Like，MongoDB的全文搜索对于文本搜索效率要高于SQL的like实现。

## 准备测试数据

往stores集合写入几条数据
```terminal
    db.stores.insert(
        [
            { _id: 1, name: "Java Hut", description: "Coffee and cakes" },
            { _id: 2, name: "Burger Buns", description: "Gourmet hamburgers" },
            { _id: 3, name: "Coffee Shop", description: "Just coffee" },
            { _id: 4, name: "Clothes Clothes Clothes", description: "Discount clothing" },
            { _id: 5, name: "Java Shopping", description: "Indonesian goods" }
        ]
    )
```

## 创建文本索引

要想使用全文搜索的能力，需要创建text索引。

例子：
```terminal
    db.stores.createIndex( { name: "text", description: "text" } )
```

- 说明：
    - 为name和description两个字段创建text类型索引。

?>提示：一个集合只允许一个text索引，但是一个text索引可以包括多个字段。

## $text操作符

通过$text操作符使用文本搜索。

语法：
```terminal
    { $text: { $search: "搜索关键词" }
```

例子：
```terminal
    db.stores.find( { $text: { $search: "java coffee shop" } } )
```

- 说明：
    - 搜索name和description字段内容，包括”coffee”, “shop”, 和 “java”关键词的文档。

输出:
```terminal
    { "_id" : 3, "name" : "Coffee Shop", "description" : "Just coffee" }
    { "_id" : 1, "name" : "Java Hut", "description" : "Coffee and cakes" }
    { "_id" : 5, "name" : "Java Shopping", "description" : "Indonesian goods" }
```

## 短语搜索
```terminal
    db.stores.find( { $text: { $search: "\"coffee shop\"" } } )
```

输出
```terminal
    { "_id" : 3, "name" : "Coffee Shop", "description" : "Just coffee" }
```

## 相关性排序

$text查询，默认返回结果是未排序的，$text查询为每一个文档的相关度都计算了一个分数(textScore)，我们可以使用这个分数来排序，让相关度高的排在前面。
```terminal
    db.stores.find(
        { $text: { $search: "java coffee shop" } },
        { score: { $meta: "textScore" } }  // 声明，返回textScore相关性分数
    ).sort( { score: { $meta: "textScore" } } ) // 指定使用textScore排序
```

?>提示: MongoDB 全文搜索对中文词组支持不太好。