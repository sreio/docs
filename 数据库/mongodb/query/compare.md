本章介绍，mongoDB的条件查询，类似SQL语句中的等值、大于、小于等等比较运算符。

## MongoDB支持的条件操作符
|         操作符         |       描述        
| :-----------: | :------------------------------: | 
|   $eq         |  等值查询，类似SQL的 = 号
|   $gt         |  大于，类似SQL的 > 号  
|   $gte        |  大于等于，类似SQL的 >= 号   
|   $in         |  匹配数组，其中一个值，类似SQL的 in 查询   
|   $lt         |  小于，类似SQL的 号     
|   $nin        |  不匹配数组中的任何值，类似SQL的 not in 号 

## 测试数据

inventory 集合数据如下
```terminal
    { _id: 1, item: { name: "ab", code: "123" }, qty: 15, tags: [ "A", "B", "C" ] }
    { _id: 2, item: { name: "cd", code: "123" }, qty: 20, tags: [ "B" ] }
    { _id: 3, item: { name: "ij", code: "456" }, qty: 25, tags: [ "A", "B" ] }
    { _id: 4, item: { name: "xy", code: "456" }, qty: 30, tags: [ "B", "A" ] }
    { _id: 5, item: { name: "mn", code: "000" }, qty: 20, tags: [ [ "A", "B" ], "C" ] }
```

## $eq (等值匹配)
```terminal
    db.inventory.find( { qty: { $eq: 20 } } )
    db.inventory.find( { "item.name": { $eq: "ab" } } )

    // 简写，可以忽略$eq操作符,  qty = 20
    db.inventory.find( { qty: 20 } )
```
等价SQL：
```terminal
    select * from inventory where qty = 20
    // 这里是举个例子，SQL不支持item.name这种，点连接嵌套字段的格式
    select * from inventory where item.name = 20
```

## $gt (大于)
```terminal
    db.inventory.find( { qty: { $gt: 20 } } )
```
等价SQL：
```terminal
    select * from inventory where qty > 20
```

##  $gte (大于等于)
```terminal
    db.inventory.find( { qty: { $gte: 20 } } )
```
等价SQL：
```terminal
    select * from inventory where qty >= 20
```

##  $in

匹配数组中，其中一个值即可
```terminal
    db.inventory.find( { qty: { $in: [ 5, 15 ] } } )
```
等价SQL：
```terminal
    select * from inventory where qty in (5, 15)
```

## $nin

跟$in操作符相反
```terminal
    db.inventory.find( { qty: { $nin: [ 5, 15 ] } } )

```
等价SQL：
```terminal
    select * from inventory where qty not in (5, 15)
```

## $lt (小于)
```terminal
    db.inventory.find( { qty: { $lt: 20 } } )
```
等价SQL：
```terminal
    select * from inventory where qty  20
```