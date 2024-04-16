MongoDB Compass是一个可视化GUI工具，方便我们通过可视化页面操作MongoDB。

?>提示：推荐开发使用MongoDB Compass操作MongoDB，毕竟敲命令不是很方便。

### 安装MongoDB Compass
- Windows系统参考：<a href='https://www.tizi365.com/topic/87.html'>Windows环境安装MongoDB章节</a>
- MacOS系统参考：<a href='https://www.tizi365.com/topic/88.html'>MacOS环境安装MongoDB章节</a>

### 连接MongoDB Server
打开Compass

![img](./img/1.png ':size=80%')

如果本地安装MongoDB的时候没有设置账号和密码，直接点击Connect，使用默认连接，就可以登录本地的MongoDB。

如果设置了账号和密码,又或者你想连接到远程MongoDB，点击右上角，进入连接配置页面

![img](./img/2.png ':size=80%')

输入MongoDB连接信息

![img](./img/3.png ':size=80%')

### 主界面
![img](./img/4.png ':size=80%')

### 创建数据库
根据上图，点击进入数据库创建窗口

![img](./img/5.png ':size=80%')

?>提示：Compass创建数据库，必须同时创建一个集合，所以上图也输入了一个集合名。

### 集合操作
![img](./img/6.png ':size=80%')

### 插入数据
![img](./img/7.png ':size=80%')

输入文档JSON数据，点击Insert插入数据
![img](./img/8.png ':size=80%')

如果输入的是JSON数组，则代表插入多条数据，这里插入的是几个文档。
数据如下：
```json
[{
    "item": "journal",
    "qty": 25,
    "size": {
        "h": 14,
        "w": 21,
        "uom": "cm"
    },
    "status": "A"
}, {
    "item": "notebook",
    "qty": 50,
    "size": {
        "h": 8.5,
        "w": 11,
        "uom": "in"
    },
    "status": "A"
}, {
    "item": "paper",
    "qty": 100,
    "size": {
        "h": 8.5,
        "w": 11,
        "uom": "in"
    },
    "status": "D"
}, {
    "item": "planner",
    "qty": 75,
    "size": {
        "h": 22.85,
        "w": 30,
        "uom": "cm"
    },
    "status": "D"
}, {
    "item": "postcard",
    "qty": 45,
    "size": {
        "h": 10,
        "w": 15.25,
        "uom": "cm"
    },
    "status": "A"
}]
```

### 查询数据
![img](./img/9.png ':size=80%')

### 修改&删除数据
![img](./img/10.png ':size=80%')

### 创建索引
切换至集合索引面板

![img](./img/11.png ':size=80%')

创建索引

![img](./img/12.png ':size=80%')

### 分析查询语句性能

类似MYSQL的explain,MongoDB也支持explain语句，用来分析查询语句的性能。
![img](./img/13.png ':size=80%')
![img](./img/14.png ':size=80%')