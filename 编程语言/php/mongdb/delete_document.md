本章介绍PHP MongoDB的文档删除操作。

## 前置教程
- <a href='/#/数据库/mongodb/README'>MongoDB教程</a>
- <a href='/#/编程语言/php/mongdb/fast_induction'>PHP MongoDB 快速入门</a>

## 删除一个文档
```php
<?php
// 使用默认地址连接MongoDB，并引用test数据库中的users集合
$collection = (new MongoDB\Client)->test->users;
$collection->drop();

// 插入测试数据
$collection->insertOne(['name' => 'Bob', 'state' => 'ny']);
$collection->insertOne(['name' => 'Alice', 'state' => 'ny']);

// 根据条件删除一个文档
$deleteResult = $collection->deleteOne(['state' => 'ny']);
```

## 批量删除文档
```php
// 根据条件匹配删除匹配的文档
$deleteResult = $collection->deleteMany(['state' => 'ny']);
```