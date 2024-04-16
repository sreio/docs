本章介绍MongoDB数据库的基础操作，包括数据库的创建、删除、查询。

### 显示所有的数据库
```console
> show dbs
admin    0.000GB
config   0.000GB
local    0.000GB
tizi365  0.000GB
```

### 切换数据库
```console
use DATABASE_NAME
```

例子：
```console
>use mydb
switched to db mydb
```

### 显示当前数据库名
```console
> db
test
```

### 创建数据库

不必显式的创建数据，只要使用use切换到一个不存在的数据库，只要插入一条数据，就会自动创建数据库

例子:
```console
// 切换到一个不存在的数据库
> use mydb
switched to db mydb

// 往movie集合插入一条文档数据（movie集合不存在，也会自动创建）
> db.movie.insert({"name":"tutorials point"})

// 显示所有数据库（自动创建了mydb数据库）
> show dbs
local      0.78125GB
mydb       0.23012GB
test       0.23012GB
```
?> 提示：MongoDB的数据库和集合，都不必提前创建，在首次写入数据的时候会自动创建。

### 删除数据库

db.dropDatabase() api可以删除当前数据库

例子：
```console
// 切换到mydb数据库
> use mydb
switched to db mydb

// 删除mydb数据库
> db.dropDatabase()
> { "dropped" : "mydb", "ok" : 1 }
```