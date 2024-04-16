本章介绍，通过mongo shell更新MongoDB文档数据。

MongoDB通过各种类型的操作符实现的不同的更新方式，也支持多种执行更新的函数。

### 准备测试数据

往inventory集合插入一批测试数据。
```markdown
db.inventory.insertMany( [
   { item: "canvas", qty: 100, size: { h: 28, w: 35.5, uom: "cm" }, status: "A" },
   { item: "journal", qty: 25, size: { h: 14, w: 21, uom: "cm" }, status: "A" },
   { item: "mat", qty: 85, size: { h: 27.9, w: 35.5, uom: "cm" }, status: "A" },
   { item: "mousepad", qty: 25, size: { h: 19, w: 22.85, uom: "cm" }, status: "P" },
   { item: "notebook", qty: 50, size: { h: 8.5, w: 11, uom: "in" }, status: "P" },
   { item: "paper", qty: 100, size: { h: 8.5, w: 11, uom: "in" }, status: "D" },
   { item: "planner", qty: 75, size: { h: 22.85, w: 30, uom: "cm" }, status: "D" },
   { item: "postcard", qty: 45, size: { h: 10, w: 15.25, uom: "cm" }, status: "A" },
   { item: "sketchbook", qty: 80, size: { h: 14, w: 21, uom: "cm" }, status: "A" },
   { item: "sketch pad", qty: 95, size: { h: 22.85, w: 30.5, uom: "cm" }, status: "A" }
] );
```

### 更新操作符语法

为了更新文档，MongoDB提供了多种更新操作符（例如$set）来修改字段值。

更新操作符的参数格式如下：
```markdown
{
  <update operator>: { <field1>: <value1>, ... },
  <update operator>: { <field2>: <value2>, ... },
  ...
}
```
说明:
```markdown
<update operator> 更新操作符
<field1> 字段名
<value1> 字段参数
```

### $set操作符

    用于更新文档，如果字段不存在则创建新的字段。

### 更新单个文档

通过db.collection.updateOne()方法更新一个文档
```markdown
db.inventory.updateOne(
   { item: "paper" },
   {
     $set: { "size.uom": "cm", status: "P" },
     $currentDate: { lastModified: true }
   }
)
```
更新说明：
```markdown
首先通过查询条件(item=paper)，查询出第一个文档。
使用$set 运算符将size.uom字段的值更新为cm，将status字段的值更新为P
使用$currentDate运算符将lastModified字段的值更新为当前日期。 如果lastModified字段不存在，则$currentDate将创建该字段。
```

### 更新多个文档

使用db.collection.updateMany()方法，批量更新满足条件的文档
```markdown
db.inventory.updateMany(
   { "qty": { $lt: 50 } },
   {
     $set: { "size.uom": "in", status: "P" },
     $currentDate: { lastModified: true }
   }
)
```
更新说明：
```markdown
首先通过查询条件（qty < 50）,查询出所有文档。
使用$set运算符将size.uom字段的值更新为in，将status字段的值更新为P。
使用 $currentDate 操作符将lastModified字段的值更新为当前日期。如果lastModified字段不存在，则$currentDate 将创建该字段。
```
?> 提示：$lt操作符，表示小于号的意思。

### 替换文档

使用db.collection.replaceOne()方法，替换一个文档的内容（除了_id字段），替换文档可以跟原始文档有不同的字段。由于_id字段是不可变的，因此可以省略_id字段。但是，如果你确实包含_id字段，那么id值必须跟被更新的文档id保持一致。
```markdown
db.inventory.replaceOne(
   { item: "paper" },
   { item: "paper", instock: [ { warehouse: "A", qty: 60 }, { warehouse: "B", qty: 40 } ] }
)
```
说明：
```markdown
首先通过查询条件（item=paper），查询出首个文档。
将匹配到的文档，替换为replaceOne方法第二个参数的内容。
```

### 更新行为
- 原子性

    MongoDB中的所有写操作都是单个文档级别上的原子操作。

- _id字段

    _id字段是不可变的，无法更新。