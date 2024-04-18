本章介绍，MongoDB的分页查询，类似MYSQL分页的用法，MongoDB的分页查询通过Cursor游标的.limit和skip函数实现。

## 测试数据

往inventory集合插入几条数据
```terminal
    db.inventory.insertMany( [
        { item: "journal", status: "A", size: { h: 14, w: 21, uom: "cm" }, instock: [ { warehouse: "A", qty: 5 } ] },
        { item: "notebook", status: "A",  size: { h: 8.5, w: 11, uom: "in" }, instock: [ { warehouse: "C", qty: 5 } ] },
        { item: "paper", status: "D", size: { h: 8.5, w: 11, uom: "in" }, instock: [ { warehouse: "A", qty: 60 } ] },
        { item: "planner", status: "D", size: { h: 22.85, w: 30, uom: "cm" }, instock: [ { warehouse: "A", qty: 40 } ] },
        { item: "postcard", status: "A", size: { h: 10, w: 15.25, uom: "cm" }, instock: [ { warehouse: "B", qty: 15 }, { warehouse: "C", qty: 35 } ] }
    ]);
```

## 限制返回数据量
```terminal
    db.inventory.find({}).limit(5)
```
- 说明：
    - 通过limit函数设置，最多返回多少条数据。

## 分页
```terminal 
    db.inventory.find({}).limit(5).skip(2)
```
- 说明：
    - 通过skip设置跳过多少数据，limit限制返回多少条数据。
    - limit类似SQL的limit，skip类似SQL的offset偏移量。