 - 使用MongoDB的第一步是先了解MongoDB的基础概念，还有数据模型（数据结构），知道数据是怎么存储的，实际上MongoDB跟MYSQL非常相似，基本上大家都会MYSQL学习MongoDB不会有什么难度。

### MongoDB基础概念
 - `数据库（database）`: MongoDB数据库跟MYSQL的数据库是一个意思。

 - `集合（collection）`: MongoDB集合，指的是文档数据的集合，跟MYSQL的表是类似的概念。

 - `文档（document）`: MongoDB文档，类似MYSQL表里面的一行数据，一个文档就是一个JSON结构的数据，由多个字段组成，所以MongoDB的文档结构非常灵活。

 - `字段（field）`: MongoDB文档字段，跟MYSQL表字段是类似的概念，代表具体要存储的数据，每个字段都有自己的数据类型。

 - `索引（index）`: MongoDB索引跟MYSQL索引是类似的概念，目的都是为了提高查询效率。

 - `聚合（Aggregation ）`: MongoDB聚合概念，跟MYSQL的Group by/Count/Sum聚合分析是类似的概念，就是用来做数据统计分析的。

### MongoDB和MYSQL的概念对比
|         mysql          |       mongodb        |                                    解释                                     |
| :--------------------: | :------------------: | :-------------------------------------------------------------------------: |
|   数据库（database）   |  数据库（database）  |                          跟MYSQL的数据`库`是一个意思                          |
|      表（table）       |  集合（collection）  |                           跟MYSQL的`表`是类似的概念                           |
|       行（row）        |   文档（document）   |          类似MYSQL表里面的`一行数据`，一个文档就是一个JSON结构的数据          |
|      列（column）      |  文档字段（field）   | 跟MYSQL表`字段`是类似的概念，代表具体要存储的数据，每个字段都有自己的数据类型 |
|     索引（index）      |    索引（index）     |              跟MYSQL索引是类似的概念，目的都是为了提高查询效率              |
| 聚合（例如：group by） | 聚合（Aggregation ） |  跟MYSQL的Group by/Count/Sum聚合分析是类似的概念，就是用来做数据统计分析的  |

基本上MongoDB的概念都能和MYSQL对上，所以说MongoDB是最像关系数据库的非关系数据（NoSQL）。

### MongoDB文档数据
一个文档就是一个JSON结构的数据，由多个字段组成，每个字段都有自己的类型，使用MongoDB进行开发，基本上都是围绕文档结构怎么设计进行展开，这个跟MYSQL类似，使用MYSQL核心就是怎么设计表结构。

MongoDB 文档数据例子：
```json
{
  "_id": "5cf0029caff5056591b0ce7d",
  "firstname": "Jane",
  "lastname": "Wu",
  "address": {
    "street": "1 Circle Rd",
    "city": "Los Angeles",
    "state": "CA",
    "zip": "90404"
  },
  "hobbies": ["surfing", "coding"]
}
```
可以是任意嵌套的JSON结构，这点MYSQL表结构是做不到的，**_id字段是文档的主键，如果你不指定具体的值，MongoDB会随机生成一个唯一值**。

MongoDB数据结构跟MYSQL 表最大的区别就是，MongoDB不需要提前定义好文档结构，对MongoDB来说，你只要直接把JSON数据直接塞到集合里面就行了，至于每一次写进去的JSON数据格式是不是一样无所谓，所以我们可以每次写入到MongoDB集合里面的数据格式都可以不一样（随便加字段、删字段）。

?> 提示：虽然MongoDB的文档结构比较灵活，可以做到每一行数据格式都不一样，但是实际应用中，我们一个集合的数据结构通常都是统一的，否则的话，一个集合里面的数据，每一行数据格式都不一样，维护的人会吐血的，因为你不知道每次读出来的数据是什么格式的。