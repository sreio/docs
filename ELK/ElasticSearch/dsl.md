## 在Kibana中演示索引库的CRUD

- 创建索引库：PUT /索引库名
- 查询索引库：GET /索引库名
- 删除索引库：DELETE /索引库名
- 修改索引库（添加字段）：PUT /索引库名/_mapping

```css
PUT /索引库名称
{
  "mappings": {
    "properties": {
      "字段名":{
        "type": "text",
        "analyzer": "ik_smart"
      },
      "字段名2":{
        "type": "keyword",
        "index": "false"
      },
      "字段名3":{
        "properties": {
          "子字段": {
            "type": "keyword"
          }
        }
      },
      // ...略
    }
  }
}

```

例如：

```css
# 添加索引库
PUT /table1
{
  "mappings": {
    "properties": {
      "name": {
        "type": "text"
      },
      "sex": {
        "type": "boolean"
      },
      "phone": {
        "type": "keyword"
      },
      "age": {
        "type": "integer"
      },
      "create_date": {
        "type": "date",
        "format": "yyyy-MM-dd HH:mm:ss"
      },
      "other": {
        "properties": {
          "hobby": {
            "type": "text"
          },
          "height": {
            "type": "float"
          }
        }
      }
    }
  }
}


# 查询索引库
GET /table1

# 删除索引库
DELETE /table1

# 修改索引库
PUT /table1/_mapping
{
  "properties": {
    "update_date": {
      "type": "date",
        "format": "yyyy-MM-dd HH:mm:ss"
    }
  }
}

```


![](./img/2-2.png ':size=50%')

![](./img/2-3.png ':size=50%')

!> 注意一旦创建完mapping，就不能修改了，只能添加新的字段

![](./img/2-4.png ':size=50%')

![](./img/2-5.png ':size=50%')


## 在Kibana中演示文档的CRUD

- 创建文档：POST /{索引库名}/_doc/文档id
- 查询文档：GET /{索引库名}/_doc/文档id
- 删除文档：DELETE /{索引库名}/_doc/文档id
- 修改文档：
    - 全量修改：PUT /{索引库名}/_doc/文档id
    - 增量修改：POST /{索引库名}/_update/文档id 
      - { "doc": {字段}}

#### 修改文档有两种方式：
- `全量修改`：直接覆盖原来的文档
  1. 根据指定的id删除文档
  2. 新增一个相同id的文档
- `增量修改`：修改文档中的部分字段

!> 注意：如果根据id删除时，id不存在，第二步的新增也会执行，也就从修改变成了新增操作了。

语法如下：
```css
# 创建文档
POST /索引库名/_doc/文档id
{
    "字段1": "值1",
    "字段2": "值2",
    "字段3": {
        "子属性1": "值3",
        "子属性2": "值4"
    },
    // ...
}

# 查询文档
GET /索引库名/_doc/文档id

# 增量修改文档
POST /索引库名/_update/文档id
{
    "doc": {
        "字段名": "字段值"
        ...其他字段
    }
}

# 全量修改文档
PUT /索引库名/_doc/文档id
{
    "字段1": "值1",
    "字段2": "值2",
    "字段3": {
        "子属性1": "值3",
        "子属性2": "值4"
    },
    // ...
}

# 删除文档
DELETE /索引库名/_doc/文档id
```

示例：
```css
# 创建文档
POST /table1/_doc/1
{
  "name": "伟大大",
  "age": 18,
  "phone": "18512345678",
  "sex": true,
  "create_date": "2021-04-09 12:51:11",
  "update_date": "2021-04-09 12:51:11",
  "other": {
    "hobby": "唱歌,跳舞,Rap",
    "height": "188.88"
  }
}

# 查询文档
GET /table1/_doc/1

# 增量修改文档
POST /table1/_update/1
{
  "doc": {
    "age": 17,
    "update_date": "2021-04-09 13:52:01"
  }
}

# 全量修改文档
PUT /table1/_doc/1
{
  "name": "伟大大",
  "age": 18,
  "phone": "18512345678",
  "sex": true,
  "create_date": "2021-04-09 12:51:11",
  "update_date": "2021-04-09 12:51:11",
  "other": {
    "hobby": "唱歌,跳舞,Rap",
    "height": "188.88"
  }
}

# 删除文档
DELETE /table1/_doc/1
```

![](./img/2-6.png ':size=50%')
![](./img/2-7.png ':size=50%')
![](./img/2-8.png ':size=50%')
![](./img/2-9.png ':size=50%')