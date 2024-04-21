类似SQL语句的group by统计分析，MongoDB也支持类似的统计分析方法。本章介绍PHP MongoDB统计分析用法。

## 前置教程
- <a href='/#/数据库/mongodb/README'>MongoDB教程</a>
- <a href='/#/编程语言/php/mongdb/fast_induction'>PHP MongoDB 快速入门</a>

## PHP MongoDB统计分析

通过aggregate执行统计分析语句
```php
<?php
// 从test数据库，引用zips集合
$collection = (new MongoDB\Client)->test->zips;

// 执行统计分析
$cursor = $collection->aggregate([
    ['$group' => ['_id' => '$state', 'count' => ['$sum' => 1]]], // 设置分组条件，类似SQL的group by
    ['$sort' => ['count' => -1]], // 设置排序条件
    ['$limit' => 5], // 设置最多返回几条记录
]);

// 遍历查询结果
foreach ($cursor as $state) {
    printf("%s has %d zip codes\n", $state['_id'], $state['count']);
}
```

?>提示：更多MongoDB统计分析语法，请参考MongoDB聚合分析章节