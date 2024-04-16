本章介绍，通过mongo shell，删除MongoDB文档。

### 准备测试数据

往insertMany集合插入一批测试数据。
```markdown
db.inventory.insertMany( [
   { item: "journal", qty: 25, size: { h: 14, w: 21, uom: "cm" }, status: "A" },
   { item: "notebook", qty: 50, size: { h: 8.5, w: 11, uom: "in" }, status: "P" },
   { item: "paper", qty: 100, size: { h: 8.5, w: 11, uom: "in" }, status: "D" },
   { item: "planner", qty: 75, size: { h: 22.85, w: 30, uom: "cm" }, status: "D" },
   { item: "postcard", qty: 45, size: { h: 10, w: 15.25, uom: "cm" }, status: "A" },
] );
```

### 删除所有文档

删除inventory集合的所有文档
```markdown
db.inventory.deleteMany({})
```
这里传入了一个空的查询条件 {}

### 根据条件删除一批文档
```markdown
db.inventory.deleteMany({ status : "A" })
```
说明：
```markdown
删除inventory集合中，条件匹配：字段status=A的文档
deleteMany函数，支持查询条件。
```

### 根据条件删除一个文档
```markdown
db.inventory.deleteOne( { status: "D" } )
```
说明:
```markdown
删除inventory集合中，条件匹配：字段status=D的第一个文档
```