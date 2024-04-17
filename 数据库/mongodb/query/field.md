本章介绍，MongoDB查询，如何设置返回指定字段，而不是返回全部字段数据。

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

## 格式
```terminal
    {<field>: 1 or 0}
```

- 说明
    - `<field>` - 字段名
    - `1` 代表返回字段，`0` 代表排除字段（即不返回）

?> 提示：使用true/false代替1和0也行。

## 返回指定字段

通过find函数的第二个参数设置查询返回那些字段。
```terminal
    db.inventory.find( { status: "A" }, { item: 1, status: 1 } )
```
- 说明：
    - 查询条件满足status=A的文档
    - 返回_id, item和status字段。

?> 提示：_id字段是默认返回的。

等价SQL：
```terminal
    SELECT item, status from inventory WHERE status = "A"
```

## 排除指定字段
```terminal
    db.inventory.find( { status: "A" }, { status: 0, instock: 0 } )
```
- 说明：
    - 查询条件满足status=A的文档
    - 返回除了item和status字段以外的所有数据

## 返回嵌套文档中的指定字段
```terminal
    db.inventory.find(
        { status: "A" },
        { item: 1, status: 1, "size.uom": 1 }
    )
```
- 说明：
    - size.uom，代表返回size下面的uom字段</field></field>