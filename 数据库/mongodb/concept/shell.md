### Mongo Shell交互式命令窗口
mongo shell是MongoDB的交互式命令窗口。可以使用mongo shell操作MongoDB，例如：对MongoDB进行各种CRUD操作。 MongoDB 安装的时候就自带了mongo shell，不用单独安装。

?>提示：后续的章节，主要使用mongo shell 命令和api介绍MongoDB各种操作，如果你使用编程语言或者可视化的MongoDB Compass操作MongoDB，他们语法都是类似的，所以只要掌握mongo shell的语法，就知道其他工具的使用方式。

### 启动mongo Shell并连接到MongoDB
- 连接本地MongoDB Server ，直接输入mongo命令，即可进入Mongo Shell
  
```terminal
mongo
```

- 使用默认的地址连接MongoDB Serve

?> 提示：如果提示找不到mongo命令，那说明安装MongoDB的时候，没有将MongoDB的bin目录添加到PATH环境变量，具体可以参考前面的安装章节。

- 成功则输出如下信息
```terminal
MongoDB shell version v4.4.5
connecting to: mongodb://127.0.0.1:27017/?compressors=disabled&gssapiServiceName=mongodb
Implicit session: session { "id" : UUID("b11bfc3e-e70c-42b1-9bfc-5d9218f2d232") }
MongoDB server version: 4.4.5
>
```
可以在交互式窗口中，输入操作命令。

- 进入docker容器中的mongo shell

    如果，我们使用docker安装MongoDB的容器名字叫做mongo，则使用下面命令可以直接进入mongo shell
```terminal
docker exec -it mongo mongo
```

- 连接远程MongoDB Server
```terminal
mongo --username root --password  --host mongodb0.examples.com --port 28015
```
    - 参数说明：
        - username 设置MongoDB账号为root
        - password 会提示你输入密码
        - host 指定mongodb服务器地址
        - port 指定MongoDB Server端口号

### mongo shell的基本命令
- 显示当前使用的数据库名
```terminal
    db
```

- 切换到其他数据库
```terminal
    use 数据库名
```

- mongo shell操作例子
```terminal
    // 切换数据库
    use myNewDatabase

    // 插入一条数据
    db.myCollection.insertOne( { x: 1 } );

    // 查询inventory集合全部数据
    db.inventory.find( {} )

    // 查询inventory集合，status = "D"的文档数据
    db.inventory.find( { status: "D" } )
```
更多的mongo Shell操作命令，后续章节会有介绍。

- 退出mongo shell
```terminal
    <ctrl-c></ctrl-c>
```