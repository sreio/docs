本章节介绍MongoDB 通过mongo shell方式插入文档数据。

### 插入一条数据

db.collection.insertOne() 将单个文档插入集合中。

如果文档没有指定 _id字段，那么MongoDB会自动为_id字段，生成一个唯一的ObjectId值。

?> 说明：ObjectId是MongoDB是内置唯一ID生成器，用于ID生成。

例子：

往inventory集合插入一个文档。
```console
db.inventory.insertOne(
   { item: "canvas", qty: 100, tags: ["cotton"], size: { h: 28, w: 35.5, uom: "cm" } }
)
```

返回：
```console
{
    "acknowledged" : true,
    "insertedId" : ObjectId("609bf11dfc901345cafc438a")
}
```

插入成功则返回主键Id, insertedId字段就是MongoDB自动生成的唯一Id，如果inventory集合不存在，则自动创建inventory集合。

查询刚才插入的文档数据
```console
> db.inventory.find( { item: "canvas" } )
{ "_id" : ObjectId("609bf11dfc901345cafc438a"), "item" : "canvas", "qty" : 100, "tags" : [ "cotton" ], "size" : { "h" : 28, "w" : 35.5, "uom" : "cm" } }
```
通过find方法，输入查询条件, 查询item=canvas的文档数据。

### 插入多条数据

db.collection.insertMany() 方法可以将多个文档插入一个集合中。

例子：

插入三个文档数据，给insertMany方法传递一个数组即可
```console
db.inventory.insertMany([
   { item: "journal", qty: 25, tags: ["blank", "red"], size: { h: 14, w: 21, uom: "cm" } },
   { item: "mat", qty: 85, tags: ["gray"], size: { h: 27.9, w: 35.5, uom: "cm" } },
   { item: "mousepad", qty: 25, tags: ["gel", "blue"], size: { h: 19, w: 22.85, uom: "cm" } }
])
```

返回值
```console
{
    "acknowledged" : true,
    "insertedIds" : [
        ObjectId("609bf30ffc901345cafc438b"),
        ObjectId("609bf30ffc901345cafc438c"),
        ObjectId("609bf30ffc901345cafc438d")
    ]
}
```
返回新插入的三个文档的id。

### 插入行为
- 集合创建

    MongoDB不用提前创建集合，首次插入数据的时候，如果集合不存在，则自动创建一个。

- _id字段

    在MongoDB中，存储在集合中的每个文档都有一个唯一的_id字段作为主键。 如果插入的文档省略_id字段，则MongoDB驱动程序会自动为_id字段生成ObjectId（唯一ID）。
    
- 原子性    

    MongoDB中的所有写操作都是单个文档级别的原子操作。