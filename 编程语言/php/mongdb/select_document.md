本章介绍PHP MongoDB文档查询的用法。

## 前置教程
- <a href='/#/数据库/mongodb/README'>MongoDB教程</a>
- <a href='/#/编程语言/php/mongdb/fast_induction'>PHP MongoDB 快速入门</a>

?>提示：不了解MongoDB，请先阅读MongoDB教程，MongoDB查询语法，在MongoDB教程中查询相关章节有介绍，PHP的查询语法跟MongoDB原生语法一样，区别只是换成PHP数组格式表达。

## 查询一个文档
使用findOne查询一个文档
```php
<?php
// 从test数据库，引用zips集合(集合可以不存在)
$collection = $client->test->zips;
// 根据查询条件查询一个文档,  条件: _id = 94301
$document = $collection->findOne(['_id' => '94301']);

// 打印文档数据
var_dump($document);
```

输出：
```terminal
object(MongoDB\Model\BSONDocument)#13 (1) {
  ["storage":"ArrayObject":private]=>
  array(5) {
    ["_id"]=>
    string(5) "94301"
    ["city"]=>
    string(9) "PALO ALTO"
    ["loc"]=>
    object(MongoDB\Model\BSONArray)#12 (1) {
      ["storage":"ArrayObject":private]=>
      array(2) {
        [0]=>
        float(-122.149685)
        [1]=>
        float(37.444324)
      }
    }
    ["pop"]=>
    int(15965)
    ["state"]=>
    string(2) "CA"
  }
}
```

## 查询多个文档

通过find函数查询多个文档
```php
// 等价SQL： select * from zips where city = 'JERSEY CITY' and state= 'NJ'
$cursor = $collection->find(['city' => 'JERSEY CITY', 'state' => 'NJ']);

// 遍历文档数据
foreach ($cursor as $document) {
    // 打印文档ID
    echo $document['_id'], "\n";
}
```

## 返回指定字段

类似SQL也可以指定返回的字段，通过projection参数设置需要返回的字段
```php
<?php
// 引用restaurants集合
$collection = $client->test->restaurants;

$cursor = $collection->find(
    [ // 设置查询条件, cuisine = 'Italian' and borough = 'Manhattan'
        'cuisine' => 'Italian',
        'borough' => 'Manhattan',
    ],
    [ // 设置可选参数
        'projection' => [ // 通过projection参数设置返回那些字段
            'name' => 1, // 格式: 字段名 => 1  , 1 代表需要返回字段
            'borough' => 1,
            'cuisine' => 1,
        ]
    ]
);

// 遍历文档
foreach($cursor as $restaurant) {
   // 打印文档内容
   var_dump($restaurant);
};
```

## 分页查询&排序

类似SQL语句的limit和offset，MongoDB也支持分页查询和排序
```php
<?php
$cursor = $collection->find(
    [], // 设置查询条件，为空代表查询全部数据
    [ // 附加参数
        'limit' => 5, // 类似SQL的limit，限制最多返回多少条记录，相当于分页大小
        'skip' => 0, // 类似SQL的offset，设置跳过多少条记录，跟limit配合可以实现分页效果
        // 类似SQL的order by， 设置根据那个字段排序
        'sort' => ['pop' => -1], // 根据pop字段逆序排列，-1代表逆序，1代表正序
    ]
);
```

## 组合查询条件
### 关系运算符
等值查询
```php
// status = 'D'
$cursor = $collection->find(['status' => 'D']);
// 使用$eq操作符, 跟上面是等价的
$cursor = $collection->find(['status' => ['$eq' =>  'D']]);
```

in查询
```php
// status in ("A", "D")
$cursor = $collection->find(['status' => ['$in' => [ "A", "D" ]]]);
```

综合例子
```php
$cursor = $collection->find([
    'qty' => ['$gt' => 20], //  qty > 20
    'qty' => ['$lt' => 100], // qty < 100
    'status' => 'D' // status = 'D'
]);
```

等价SQL条件：
```terminal
qty > 20 and qty < 100 and status = 'D'
```

?>提示：更多查询语法，请参考MongoDB教程

### 逻辑运算符

MongoDB也支持and和or，他们的语法格式是一样的。
```php
$cursor = $collection->find([
    '$or' => [ // 操作符
        ['status => 'D'], // 条件1： status = 'D'
        ['status' => 'A'] // 条件2： status = 'A'
    ]
]);
```
等价SQL条件:
```terminal
status = ‘D’ or status = ‘A’
```
?>注意：$and条件，跟$or语法类似，上面的例子把or改成and即可。