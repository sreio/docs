本章介绍，MongoDB查询结果排序，类似MYSQL order by的用法，MongoDB的分页查询通过Cursor游标的sort函数实现。

## 准备测试数据

往restaurants集合写入几条数据
```terminal
    db.restaurants.insertMany( [
        { "_id" : 1, "name" : "Central Park Cafe", "borough" : "Manhattan"},
        { "_id" : 2, "name" : "Rock A Feller Bar and Grill", "borough" : "Queens"},
        { "_id" : 3, "name" : "Empire State Pub", "borough" : "Brooklyn"},
        { "_id" : 4, "name" : "Stan's Pizzaria", "borough" : "Manhattan"},
        { "_id" : 5, "name" : "Jane's Deli", "borough" : "Brooklyn"},
    ] );
```

## 对结果排序
```terminal
    db.restaurants.find({}).sort({_id:1})
```
- 说明：
    - 通过sort函数，设置排序字段。
    - 查询所有数据且根据_id排序（升序）

#### sort函数参数格式:
```terminal
    <field>: 1 or -1
```
- 说明：
    - 1 代表升序
    - -1 代表降序

## 配合分页排序
```terminal
    db.restaurants.find({}).limit(2).skip(2).sort({_id:-1})
```
- 说明：
    - 查询所有数据，最多返回2条数据，跳过2条数据，根据_id排序（降序）