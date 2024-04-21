类似SQL语句的Group by统计分析，MongoDB也支持，本章介绍Golang如果实现对MongoDB数据进行统计分析。

## 前置教程

请先阅读下面前置教程
- <a href='/#/数据库/mongodb/polymerization/pipeline'>MongoDB统计分析语法</a>
- <a href='/#/编程语言/golang/MongoDB/fast_induction'>Golang MongoDB快速入门</a>

## 测试数据
orders集合数据如下
```terminal
{ _id: 1, cust_id: "abc1", ord_date: ISODate("2012-11-02T17:04:11.102Z"), status: "A", amount: 50 }
{ _id: 2, cust_id: "xyz1", ord_date: ISODate("2013-10-01T17:04:11.102Z"), status: "A", amount: 100 }
{ _id: 3, cust_id: "xyz1", ord_date: ISODate("2013-10-12T17:04:11.102Z"), status: "D", amount: 25 }
{ _id: 4, cust_id: "xyz1", ord_date: ISODate("2013-10-11T17:04:11.102Z"), status: "D", amount: 125 }
{ _id: 5, cust_id: "abc1", ord_date: ISODate("2013-11-12T17:04:11.102Z"), status: "A", amount: 25 }
```

## 分组统计
通过Collection.Aggregate函数执行统计查询。

下面是MongoDB一个统计分析的例子
```terminal
[
    { $match: { status: "A" } },  // 第一个阶段, 根据查询条件匹配文档数据
    { $group: { _id: "$cust_id", total: { $sum: "$amount" } } },   // 第二个阶段，分组统计
    { $sort: { total: -1 } }  // 第三个阶段，对结果排序
]
```

等价SQL：
```terminal
select sum(amount) as total from orders 
        where status="A" 
        group by cust_id 
        order by total desc
```

下面是Golang的写法
```go
package main

import (
    "context"
    "fmt"
    "log"
    "time"

    "go.mongodb.org/mongo-driver/bson"
    "go.mongodb.org/mongo-driver/mongo"
    "go.mongodb.org/mongo-driver/mongo/options"
)

func main() {
    var coll *mongo.Collection

    // 这里忽略连接MongoDB、获取集合对象的代码，可以参考快速入门教程。

    // 统计分析表达式, 跟MongoDB原生语法是一样的，只是使用Golang 数据结构表示。
    // 不了解Golang mongoDB 数据模型，请阅读前面的章节。
    pipeline := mongo.Pipeline{
            {{"$match", bson.D{{"status", "A"}}}},
            {{"$group", bson.D{{"_id", "$cust_id"}, {"total", bson.D{{"$sum", "$amount"}}}}}},
            {{"$sort", bson.D{{"total", -1}}}}
    }

    // 设置超时时间
    opts := options.Aggregate().SetMaxTime(2 * time.Second)

    // 执行统计分析
    cursor, err := coll.Aggregate(
                                context.TODO(),  // 上下文参数
                                pipeline,  // 统计分析表达式
                                opts // 可选参数
                            )
    if err != nil {
        log.Fatal(err)
    }

    // 定义结果集合，这里使用bson.M类型（是一种map结构）存储查询结果
    var results []bson.M
    // 获取所有结果，保存至results变量
    if err = cursor.All(context.TODO(), &results); err != nil {
        log.Fatal(err)
    }

    // 遍历查询结果
    for _, result := range results {
        fmt.Printf("id =  %v , total =  %v \n", result["_id"], result["total"])
    }
}
```

?>提示：更多MongoDB统计分析语法，请参考MongoDB统计分析语法