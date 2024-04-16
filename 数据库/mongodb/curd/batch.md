本章介绍，通过mongo shell,批量操作（bulkWrite）MongoDB文档数据。这里的批量操作不仅仅是前面章节提到的批量更新文档，MongoDB批量操作支持同时执行一批写操作，写操作包括：插入文档、更新文档、删除文档。

mongo shell通过db.collection.bulkWrite()函数执行批量操作。

?>提示： 批量操作，经常使用在批量同步数据场景。

### bulkWrite函数支持的写操作

批量操作支持下面写操作自由组合。
```markdown
insertOne - 插入一个文档
updateOne - 更新一个文档
updateMany - 更新一批文档
replaceOne - 替换一个文档
deleteOne - 删除一个文档
deleteMany - 删除一批文档
```

### 语法格式
```markdown
db.collection.bulkWrite(
   [ <operation 1>, <operation 2>, ... ],
)
```
说明：
```markdown
operation - 代表写操作配置
bulkWrite接收一个写操作数组。
```

### 例子

下面看一个综合里面，批量执行一批文档写操作。
```markdown
db.inventory.bulkWrite(
      [
         // 插入一个文档
         { insertOne :
            {
            // 文档内容
               "document" :
               {
                  "_id" : 4, "char" : "Dithras", "class" : "barbarian", "lvl" : 4
               }
            }
         },
         { insertOne :
            {
               "document" :
               {
                  "_id" : 5, "char" : "Taeln", "class" : "fighter", "lvl" : 3
               }
            }
         },
         // 更新一个文档，updateMany 更新一批文档类似
         { updateOne :
            {
               // 更新条件
               "filter" : { "char" : "Eldon" },
               // 更新内容
               "update" : { $set : { "status" : "Critical Injury" } }
            }
         },
         // 删除一个文档，deleteMany删除多个文档类似
         { deleteOne :
            // 删除条件
            { "filter" : { "char" : "Brisbane" } }
         },
         // 替换一个文档
         { replaceOne :
            {
                // 替换条件
               "filter" : { "char" : "Meldane" },
               // 替换内容
               "replacement" : { "char" : "Tanys", "class" : "oracle", "lvl" : 4 }
            }
         }
      ]
   );
```