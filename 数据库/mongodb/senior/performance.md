本章介绍MongoDB查询性能分析，类似SQL的explain，MongoDB也支持explain分析查询语句的性能。

## 基本用法

调用explain函数，可以获取分析结果
```terminal
    // find方法分析结构
    db.collection.find({}).explain();

    // aggregate方法的分析结果
    db.collection.explain().aggregate([]);

```

- explain有三种模式：
    - queryPlanner (默认)
    - executionStats
    - allPlansExecution

- 说明:
    - 使用queryPlanner只列出所有可能执行的方案，不会执行实际的语句，显示已经胜出的方案winningPlan。
    - 使用executionStats只执行winningPlan方案，并输出结果。
    - 使用allPlansExecution执行所有的方案，并输出结果。

不用模式的用法
```terminal
    // executionStats 模式, 给explain函数传递参数即可
    db.collection.find({}).explain('executionStats');

    // allPlansExecution 模式
    db.collection.find({}).explain('allPlansExecution');
```

## explain内容解读
### queryPlanner内容

下面内容是explain默认返回内容，忽略了非关键信息
```terminal
{
    "queryPlanner" : {
        "plannerVersion" : 1,
        "namespace" : "test.orders",
        "indexFilterSet" : false, // 关键指标，有没有使用索引过滤数据
        "winningPlan" : {
            "stage" : "COLLSCAN",  // 关键指标，stage阶段名称。每个阶段都有每个阶段特有的信息，例如：COLLSCAN代表扫描整个集合内容
            "direction" : "forward"
        },
        "rejectedPlans" : [ ]
    }
    ...
}
```

- stage阶段类型如下：

    - `COLLSCAN：`全表扫描
    - `IXSCAN：`索引扫描
    - `FETCH：`根据索引去检索指定document
    - `SHARD_MERGE：`将各个分片返回数据进行merge
    - `SORT：`表明在内存中进行了排序
    - `LIMIT：`使用limit限制返回数
    - `SKIP：`使用skip进行跳过
    - `IDHACK：`针对_id进行查询
    - `SHARDING_FILTER：`通过mongos对分片数据进行查询
    - `COUNT：`利用db.coll.explain().count()之类进行count运算
    - `COUNTSCAN：` count不使用Index进行count时的stage返回
    - `COUNT_SCAN：` count使用了Index进行count时的stage返回
    - `SUBPLA：`未使用到索引的$or查询的stage返回
    - `EXT：`使用全文索引进行查询时候的stage返回
    - `PROJECTION：`限定返回字段时候stage的返回

### executionStats内容

下面内容是executionStats模式返回内容，忽略了非关键信息
```terminal
{
    "executionStats" : {
        "executionSuccess" : true,
        "nReturned" : 5,  // 返回文档数
        "executionTimeMillis" : 0, // 执行时间 
        "totalKeysExamined" : 0, // 扫描多少个索引
        "totalDocsExamined" : 5, // 总扫描文档数
        "executionStages" : {
            "stage" : "COLLSCAN", // 阶段类型， COLLSCAN全表扫描的意思
            "nReturned" : 5,
            "executionTimeMillisEstimate" : 0,
            "works" : 7,
            "advanced" : 5,
            "needTime" : 1,
            "needYield" : 0,
            "saveState" : 0,
            "restoreState" : 0,
            "isEOF" : 1,
            "direction" : "forward",
            "docsExamined" : 5
        }
    }
}
```

## 查询优化思路
- 尽量使用索引
- 扫描文档数越小越好