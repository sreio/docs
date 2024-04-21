本章介绍PHP MongoDB的文档写入操作。

## 前置教程
- <a href='/#/数据库/mongodb/README'>MongoDB教程</a>
- <a href='/#/编程语言/php/mongdb/fast_induction'>PHP MongoDB 快速入门</a>

## 插入一个文档
```php
<?php
// 从test数据库，引用users集合
$collection = $client->test->users;

// 插入一个文档，无须提前定义结构，直接写入数据。
$insertOneResult = $collection->insertOne([
    'username' => 'admin',
    'email' => 'admin@example.com',
    'name' => 'Admin User',
]);

// 打印插入行数
printf("Inserted %d document(s)\n", $insertOneResult->getInsertedCount());

// 获取文档ID （自动生成的ID）
var_dump($insertOneResult->getInsertedId());
```

输出:
```terminal
Inserted 1 document(s)
object(MongoDB\BSON\ObjectId)#11 (1) {
  ["oid"]=>
  string(24) "579a25921f417dd1e5518141"
}
```

也可以手动指定文档ID
```php
// 设置_id属性值
$insertOneResult = $collection->insertOne(['_id' => 1, 'name' => 'Alice']);

// 打印文档ID
var_dump($insertOneResult->getInsertedId());
```

输出
```terminal
int(1)
```

## 批量插入文档
通过insertMany函数，批量插入文档
```php
<?php
// 传入多个文档数据即可
$insertManyResult = $collection->insertMany([
    [
        'username' => 'admin',
        'email' => 'admin@example.com',
        'name' => 'Admin User',
    ],
    [
        'username' => 'test',
        'email' => 'test@example.com',
        'name' => 'Test User',
    ],
]);

// 打印插入行数
printf("Inserted %d document(s)\n", $insertManyResult->getInsertedCount());

// 获取刚才插入的文档ID
var_dump($insertManyResult->getInsertedIds());
```

输出
```terminal
Inserted 2 document(s)
array(2) {
  [0]=>
  object(MongoDB\BSON\ObjectId)#11 (1) {
    ["oid"]=>
    string(24) "579a25921f417dd1e5518141"
  }
  [1]=>
  object(MongoDB\BSON\ObjectId)#12 (1) {
    ["oid"]=>
    string(24) "579a25921f417dd1e5518142"
  }
}
```