本章节介绍MongoDB统计分析详解，主要通过Aggregation Pipeline (聚合管道) 实现，使用上类似SQL的group by语句，Mongo shell通过db.collection.aggregate()函数实现统计分析。

## 前置教程
<a href='/#/数据库/mongodb/polymerization/pipeline.md'>MongoDB聚合管道(Aggregation Pipeline)</a>

## 常规步骤

1. 通过$match筛选目标数据    
2. 通过$group对数据进行分组统计
3. 通过$sort对结果进行排序（可选）

## 测试数据

orders集合数据如下
```terminal
    { _id: 1, cust_id: "abc1", ord_date: ISODate("2012-11-02T17:04:11.102Z"), status: "A", amount: 50 }
    { _id: 2, cust_id: "xyz1", ord_date: ISODate("2013-10-01T17:04:11.102Z"), status: "A", amount: 100 }
    { _id: 3, cust_id: "xyz1", ord_date: ISODate("2013-10-12T17:04:11.102Z"), status: "D", amount: 25 }
    { _id: 4, cust_id: "xyz1", ord_date: ISODate("2013-10-11T17:04:11.102Z"), status: "D", amount: 125 }
    { _id: 5, cust_id: "abc1", ord_date: ISODate("2013-11-12T17:04:11.102Z"), status: "A", amount: 25 }
```

## aggregate函数
```terminal
    db.collection.aggregate(pipeline)
```

- 说明：
    - pipeline 输入一个数组参数，数组每一个元素代表一个处理阶段。

例子
```terminal
db.orders.aggregate([
                     { $match: { status: "A" } },  // 第一个阶段
                     { $group: { _id: "$cust_id", total: { $sum: "$amount" } } },   // 第二个阶段
                     { $sort: { total: -1 } }  // 第三个阶段
                   ])
```

等价SQL
```terminal
select sum(amount) as total from orders 
        where status="A" 
        group by cust_id 
        order by total desc
```

## $match阶段

格式:
```terminal
{ $match: { <query> } }
```

- 说明：
    - `<query>` MongoDB查询条件
    - 用于设置查询条件，如果忽略$match，那意味着查询全部数据。

?>提示：不了解MongoDB查询语法，请考察前面的章节。

## $group阶段

类似SQL的group by子句，用于对数据进行分组，然后对分组的数据做一系列的统计计算。

### $group基础用法

语法：
```terminal
{
  $group:
    {
      _id: <expression>, // 分组条件，例如：根据那个字段进行分组
      <field1>: { <accumulator1> : <expression1> },  // 聚合运算，可以添加N个聚合运算
      ...
    }
 }
```

- 说明：
    - `<field1>` - 自定义统计指标的名字, 可以有N个
    - `<accumulator1>` - 聚合函数，类似SQL的sum、avg等聚合函数，区别是MongoDB的聚合函数以$为前缀命名，例如：$sum、$avg
    - `<expression1>` - 聚合函数的参数，通常是需要统计的字段值，引用文档字段使用 “$字段名” 格式

例子:
```terminal
db.orders.aggregate([
                     {
                         $group: { 
                            _id: "$cust_id",
                            total: { $sum: "$amount" }, // 添加第一个计算指标total，使用$sum求和操作符
                            amount_avg: {$avg: "$amount"}  // 添加第二个计算指标avg，使用$avg平均值计算操作符
                        } 
                    }
               ])
```

输出:
```terminal
    { "_id" : "abc1", "total" : 75, "amount_avg" : 37.5 }
    { "_id" : "xyz1", "total" : 250, "amount_avg" : 83.33333333333333 }
```

等价SQL
```terminal
    select 
        sum(amount) as  total,
        avg(amount) as amount_avg
    from orders 
    group by cust_id
```

### $group聚合函数

$group常用聚合函数如下：

|         操作符          |       说明        |                                    例子                                     |
| :--------------------: | :------------------: | :-------------------------------------------------------------------------: |
|    $avg     |  	           计算平均值              |                         {$avg: “$amount”}                          |
|    $sum     | 	             求和                 |                        	{$sum: “$amount”}                           |
|    $max     | 	            最大值                |                         {$max: “$amount”}                           |
|    $min     |                 最小值                | 	                    {$min: “$amount”}                           |
|    $first   |    返回分组后的数据，第一个文档的内容    |                         {$first: “$amount”}                         |
|    $last    |   返回分组后的数据，最后一个文档的内容   |                         {$last: “$amount”}                          |
|    $push    | 		     返回分组后的数据          |  	    { $push: { ord_date: “$ord_date”, amount: “$amount” }        |
|  $addToSet  |  返回分组后的数据，跟$push的区别是去重了 |  	                { $addToSet: “$amount” }                       |

### $push例子
```terminal
db.orders.aggregate(
   [
     {
       $group:
         {
           _id: "$cust_id",
           all: { $push: { ord_date: "$ord_date", amount: "$amount" } } // ord_date和amount字段值
         }
     }
   ]
)
```

输出
```terminal
{ "_id" : "abc1", "all" : [ { "ord_date" : "2021-04-18 00:00:00", "amount" : 50 }, { "ord_date" : "2021-04-21 00:00:00", "amount" : 25 } ] }
{ "_id" : "xyz1", "all" : [ { "ord_date" : "2021-04-18 00:00:00", "amount" : 100 }, { "ord_date" : "2021-04-20 00:00:00", "amount" : 25 }, { "ord_date" : "2021-04-21 00:00:00", "amount" : 125 } ] }
```

### $addToSet例子
```terminal
db.orders.aggregate(
   [
     {
       $group:
         {
           _id: "$cust_id",
           all_amount: { $addToSet: "$amount" } // 返回所有不相同的amount值
         }
     }
   ]
)
```

输出
```terminal
{ "_id" : "abc1", "all_amount" : [ 25, 50 ] }
{ "_id" : "xyz1", "all_amount" : [ 100, 25, 125 ] }
```

## $sort:

$sort阶段，通常放在最后面，用于对统计的数据进行排序

格式:
```terminal
    { $sort: { <field1>: <sort order>, <field2>: <sort order> ... } }
```

- 说明:
    - `<field1>`, `<field2>` - 需要排序的字段名，支持多个字段
    - `<sort order>` - 排序方向, -1 逆序， 1 升序

例子：
```terminal
db.orders.aggregate([
                     { $match: { status: "A" } },
                     { $group: { _id: "$cust_id", total: { $sum: "$amount" } } },
                     { $sort: { total: -1 } }
                   ])
```

## aggregate 分页处理

我们可以通过$limit和$skip操作符实现分页处理

例子:
```terminal
db.orders.aggregate([
                     { $match: { status: "A" } },
                     { $group: { _id: "$cust_id", total: { $sum: "$amount" } } },
                     { $sort: { total: -1 } },
                     { $limit: 5 }, // 限制返回多少条数据，相当于分页大小
                     { $skip: 1 } // 跳过几条记录，类似SQL的offset
                   ])
```