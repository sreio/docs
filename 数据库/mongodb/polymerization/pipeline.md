本章节介绍MongoDB Aggregation Pipeline (聚合管道)，主要用于统计分析，类似SQL的group by语句，Mongo shell通过db.collection.aggregate()函数实现统计分析。

## 概念

聚合管道（Aggregation Pipeline）是一个抽象的概念，把数据比作管道中的水，水在管道中流动，然后我们就可以对管道中的数据进行多个阶段（stage）处理，一个阶段处理完数据，再把处理的结果传给下一个阶段进行处理。

- 聚合管道在统计分析中应用例子：

    - 第一阶段，从集合中根据条件查找出一批文档数据
    - 第二阶段，对第一阶段搜出来的文档数据进行分组统计
    - 第三阶段，对二阶段统计数据进行排序

整个过程就是一个流水线式的操作，数据从管道的一头流进去，经过几个阶段处理后，输出一个最终的结果。

## MongoDB管道支持的操作符

下面是MongoDB常用的管道操作符

|         操作符         |       描述        
| :-----------: | :------------------------------: | 
|     $match     |  $match阶段，用于根据条件筛选文档数据，跟SQL的where条件类似
|     $group     |  $group阶段，用于对文档数据进行分组统计，这里跟SQL的group by类似
|     $sort      |  用于对数据进行排序   

## 聚合管道使用步骤

- 通常就是三个步骤：
    
    - 通过$match筛选目标数据
    - 通过$group对数据进行分组统计
    - 通过$sort对结果进行排序（可选）

例子:
```terminal
db.orders.aggregate([
                     { $match: { status: "A" } },
                     { $group: { _id: "$cust_id", total: { $sum: "$amount" } } },
                     { $sort: { total: -1 } }
                   ])
```

等价SQL
```terminal
select sum(amount) as total from orders 
        where status="A" 
        group by cust_id 
        order by total desc
```

?>提示：aggregate统计分析，请参考后续章节。