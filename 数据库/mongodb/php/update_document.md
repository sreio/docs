本章介绍PHP MongoDB的文档更新操作。

## 前置教程
- <a href='/#/数据库/mongodb/README'>MongoDB教程</a>
- <a href='/#/编程语言/php/mongdb/fast_induction'>PHP MongoDB 快速入门</a>

## 更新一个文档
```php
<?php
// 使用默认地址连接MongoDB，并引用test数据库中的users集合
$collection = (new MongoDB\Client)->test->users;
$collection->drop(); // 删除集合

// 下面插入2条测试数据
$collection->insertOne(['name' => 'Bob', 'state' => 'ny']);
$collection->insertOne(['name' => 'Alice', 'state' => 'ny']);

// 更新一个匹配条件的文档
$updateResult = $collection->updateOne(
    ['state' => 'ny'], // 查询条件
    // 通过$set操作符，设置需要更新的字段内容
    ['$set' => ['country' => 'us']] // 将country值改为us
);

// 打印匹配数量
printf("Matched %d document(s)\n", $updateResult->getMatchedCount());
// 打印修改数量
printf("Modified %d document(s)\n", $updateResult->getModifiedCount());
```

## 批量更新文档
```php
<?php
// 使用默认地址连接MongoDB，并引用test数据库中的users集合
$collection = (new MongoDB\Client)->test->users;
$collection->drop();

// 下面插入3个文档数据
$collection->insertOne(['name' => 'Bob', 'state' => 'ny', 'country' => 'us']);
$collection->insertOne(['name' => 'Alice', 'state' => 'ny']);
$collection->insertOne(['name' => 'Sam', 'state' => 'ny']);
// 根据条件更新多个文档内容
$updateResult = $collection->updateMany(
    ['state' => 'ny'], // 设置查询条件 state = ny
    ['$set' => ['country' => 'us']] // 将country值改为us
);
```
updateMany函数的功能跟updateOne类似，区别是updateMany支持匹配修改数据。

## 替换文档
上面的例子都是局部更新文档的字段内容，但是有时候我们想替换整个文档内容。
```php
<?php
// 使用默认地址连接MongoDB，并引用test数据库中的users集合
$collection = (new MongoDB\Client)->test->users;
$collection->drop();

// 插入测试数据
$collection->insertOne(['name' => 'Bob', 'state' => 'ny']);

// 根据查询条件，匹配一个文档内容
$updateResult = $collection->replaceOne(
    ['name' => 'Bob'], // 设置查询条件 name = 'Bob'
    ['name' => 'Robert', 'state' => 'ca'] // 替换成当前内容
);
```
执行replaceOne函数后，会将匹配到的一个文档，整个替换成replaceOne第二个参数指定的文档数据。

?>提示：MongoDB的文档ID是不能被替换的，这是个特例。

## upsert

upsert参数的作用是在执行更新操作的时候，如果要更新的文档不存在，则插入一个新的文档。
```php
<?php

$collection = (new MongoDB\Client)->test->users;
$collection->drop();

// 更新文档
$updateResult = $collection->updateOne(
    ['name' => 'Bob'], // 设置查询条件
    ['$set' => ['state' => 'ny']], // 更新内容
    // 设置可选参数upsert = true
    ['upsert' => true] // 代表如果条件name=Bob，匹配不到文档，那么将更新内容和条件插入到一个新的文档。
);

// 查询新插入的文档数据
$upsertedDocument = $collection->findOne([
    '_id' => $updateResult->getUpsertedId(),
]);

// 打印结果
var_dump($upsertedDocument);
```

输出：
```terminal
object(MongoDB\Model\BSONDocument)#16 (1) {
  ["storage":"ArrayObject":private]=>
  array(3) {
    ["_id"]=>
    object(MongoDB\BSON\ObjectId)#15 (1) {
      ["oid"]=>
      string(24) "57509c4406d7241dad86e7c3"
    }
    ["name"]=>
    string(3) "Bob"
    ["state"]=>
    string(2) "ny"
  }
}
```