本章通过mongo shell介绍，MongoDB的基本查询语法，mongo shell中提了db.collection.find()方法，用于查询数据。

## 准备测试数据

往inventory集合插入几条数据。
```terminal
db.inventory.insertMany([
   { item: "journal", qty: 25, size: { h: 14, w: 21, uom: "cm" }, status: "A" },
   { item: "notebook", qty: 50, size: { h: 8.5, w: 11, uom: "in" }, status: "A" },
   { item: "paper", qty: 100, size: { h: 8.5, w: 11, uom: "in" }, status: "D" },
   { item: "planner", qty: 75, size: { h: 22.85, w: 30, uom: "cm" }, status: "D" },
   { item: "postcard", qty: 45, size: { h: 10, w: 15.25, uom: "cm" }, status: "A" }
]);
```

## 查询全部文档

查询inventory集合中的所有文档，{} 表示传入一个空的查询条件。
```terminal
db.inventory.find( {} )
```

作用类似下面SQL语句
```terminal
SELECT * FROM inventory
```

## 等值查询条件

语法：
```terminal
{ 字段名: 匹配值, ... }
```

例子1:
```terminal
db.inventory.find( { status: "D" } )
```

作用类似下面SQL语句：
```terminal
SELECT * FROM inventory WHERE status = "D"
```

例子2：

嵌套字段查询，使用点（.）连接嵌套文档的字段即可
```terminal
db.inventory.find( { "size.uom": "in" } )
```

作用类似下面SQL语句：
```terminal
SELECT * FROM inventory WHERE size.uom = "in"
```
?> 提示：MongoDB文档是以JSON格式存储，所以支持使用嵌套字段作为查询条件。

## 使用查询操作符

MongoDB支持丰富的查询运算符，用于实现各种复杂的查询。

语法：
```terminal
{ <field1>: { <operator1>: <value1> }, ... }
```

说明:
```terminal
field1 字段名
operator1 运算符
value1 运算符参数
```

$in操作符例子:
```terminal
db.inventory.find( { status: { $in: [ "A", "D" ] } } )
```

作用类似下面SQL语句中的in查询：
```terminal
SELECT * FROM inventory WHERE status in ("A", "D")
```
?> 提示：操作符详解，请参考后续MongoDB查询详解章节。

## and条件

类似SQL, MongoDB也支持and条件。

例子：
```terminal
// 默认连续输入多个条件，条件之间使用and连接
db.inventory.find( { status: "A", item: "postcard"} )
```

作用类似下面SQL语句：
```terminal
SELECT * FROM inventory WHERE status = "A" AND item = "postcard"
```

## or条件

类似SQL, MongoDB也支持or条件, or条件是通过$or操作符实现。

语法：
```terminal
{
    $or: [
        {条件1},
        {条件2},
        .....
    ]
}
```

例子：
```terminal
db.inventory.find( { $or: [ { status: "A" }, { item: "postcard"} ] } )
```

作用类似下面SQL语句：
```terminal
SELECT * FROM inventory WHERE status = "A" OR item = "postcard"
```

## 组合and和or条件

类似SQL, MongoDB可以通过and和or一起组合复杂的查询条件

例子：
```terminal
db.inventory.find( {
     status: "A",
     $or: [ { qty: { $lt: 30 } }, { item: /^p/ } ]
} )
```
这里用到了 $lt 小于操作符，正则匹配。

作用类似下面SQL语句：
```terminal
SELECT * FROM inventory WHERE status = "A" AND ( qty  < 30 or item like 'p%')
```
?>提示：MongoDB查询条件详解请参考后续章节。