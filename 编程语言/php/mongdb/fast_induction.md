MongoDB是一个跨平台的，由C++ 语言编写，面向文档的NoSQL数据库，具备高性能、高可用和易扩展等特性，本教程从PHP语言角度描述MongoDB的使用方式。

## 1.前置教程
- <a href='/#/数据库/mongodb/README'>MongoDB教程</a>

## 2.安装依赖

PHP MongoDB依赖包括两个部分：php 扩展库和php依赖包。

PHP MongoDB扩展库提供底层API，PHP依赖包提供高层封装，方便我们开发。

?>本教程使用的是PHP 7以上的版本，MongoDB 4以上版本。

### 2.1.安装MongoDB扩展库
```terminal
sudo pecl install mongodb
```

修改php.ini配置文件，添加下面配置
```terminal
extension=mongodb.so
```

### 2.2.安装MongoDB PHP依赖包
使用composer安装依赖包
```terminal
composer require mongodb/mongodb
```

添加composer的自动加载器，负责加载MongoDB包
```php
<?php

require_once __DIR__ . '/vendor/autoload.php';
```

## 3.连接MongoDB
```php
<?php

// 创建mongodb client, 参数为MongoDB的连接配置，这里使用的默认账号和密码
$client = new MongoDB\Client(
    'mongodb://localhost:27017'
);

// 获取集合对象，格式:  $client->数据库名->集合名
$collection = $client->test->users;
```

MongoDB连接配置格式：
```terminal
mongodb://[username:password@]host1[:port1][,...hostN[:portN]][/[defaultauthdb][?options]]
```

例子:
```terminal
// 使用默认账号密码，连接localhost:27017地址
mongodb://localhost:27017

// 连接localhost:27017地址，账号=root， 密码=123456，连接默认数据库=admin
mongodb://root:123456@localhost:27017/admin
```

## 4.MongoDB CRUD
### 4.1.插入文档
```php
<?php
// 从test数据库中获取users集合
$collection = $client->test->users;

// 插入一个文档记录
$insertOneResult = $collection->insertOne([
    'username' => 'admin',
    'email' => 'admin@example.com',
    'name' => 'Admin User',
    'isVip' => 1
]);

// 打印插入文档数
printf("Inserted %d document(s)\n", $insertOneResult->getInsertedCount());

// 获取新插入文档ID
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

### 4.2.更新文档
```php
<?php
$updateResult = $collection->updateOne(
    ['username' => 'admin'], // 查询条件
    ['$set' => ['email' => 'admin@tizi365.com']] // 通过$set操作符，设置需要更新的字段内容
);

printf("Matched %d document(s)\n", $updateResult->getMatchedCount());
printf("Modified %d document(s)\n", $updateResult->getModifiedCount());
```

等价SQL：
```terminal
update users set email='admin@tizi365.com' where username='admin'
```

### 4.3.删除文档
根据条件删除文档

```php
$deleteResult = $collection->deleteOne(['username' => 'ny']);
```

等价SQL：
```terminal
delete from users where username='ny'
```

### 4.4.查询文档
```php
// 根据条件查询文档
$cursor = $collection->find(['isVip' => 1]);

// 遍历查询结果
foreach ($cursor as $document) {
    // 打印文档id
    echo $document['_id'], "\n";
}
```

等价SQL：
```terminal
select * from users where isVip=1
```