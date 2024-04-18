本章节，介绍MongoDB关于JSON文档中的数组字段的匹配操作符。

## MongoDB支持的数组查询条件
|         操作符         |       描述        
| :-----------: | :------------------------------: | 
|      $all      |  匹配查询条件中的整个数组值
|   $elemMatch   |  数组字段，只要有一个值，匹配$elemMatch设置的所有条件就成立
|     $size      |  匹配数组大小   

## $all
```terminal
    { tags: { $all: [ "ssl" , "security" ] } }
```

等价于
```terminal
    { $and: [ { tags: "ssl" }, { tags: "security" } ] }
```
tags字段是一个数组值，且tags数组同时包含ssl和security值。

## $elemMatch
scores 集合数据如下：
```terminal
    { _id: 1, results: [ 82, 85, 88 ] }
    { _id: 2, results: [ 75, 88, 89 ] }
```

例子：
```terminal
    db.scores.find(
        { results: { $elemMatch: { $gte: 80, $lt: 85 } } }
    )
```

返回数据：
```terminal
    { "_id" : 1, "results" : [ 82, 85, 88 ] }
```

- 说明：
    - results数组中的值，只要有一个值大于等于80且小于85，文档就匹配成功。

## $size

匹配数组大小
```terminal
    db.collection.find( { field: { $size: 2 } } );
```

- 说明：
    - field字段是一个数组值，只要数组大小等于2，文档就匹配成功。  