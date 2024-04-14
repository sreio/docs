### CURL

RESTful接口URL的格式：

?> http://localhost:9200/index/type/id

其中`index`、`type`是必须提供的。id是可选的，不提供es会自动生成。index、type将信息进行分层，利于管理。id相当于数据库表中记录的主键，是唯一的。

> 注：在url网址后面加"?pretty"，会让返回结果以工整的方式展示出来，适用所有操作数据类的url。"?"表示引出条件，"pretty"是条件内容。

### 通过_source获取指定的字段
```terminal
curl -XGET "http://elasticsearch:9200/table1/_search?_source=name,age,other&pretty"
```

### "q=*"表示匹配索引中所有的数据，一般默认只返回前10条数据。
```terminal
curl -XGET "http://elasticsearch:9200/table1/_search?q=*&pretty"
```
### 匹配所有数据，但只返回1个
> 注：如果size不指定，则默认返回10条数据
```terminal
curl -XGET "http://elasticsearch:9200/table1/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "size": 1
}'
```

###  返回从11到20的数据(索引下标从0开始)
```terminal
curl -XGET "http://elasticsearch:9200/table1/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "from": 10,
  "size": 10
}'
```

### 根据字段排序 (根据年龄字段降序)
```terminal
curl -XGET "http://elasticsearch:9200/table1/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "sort": {
    "age": {
      "order": "desc"
    }
  }
}'
```

### 获取指定字段 (查询name、age、create_date字段)
```terminal
curl -XGET "http://elasticsearch:9200/table1/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "_source": [
    "name",
    "age",
    "create_date"
  ]
}'
```

### 字段匹配搜索（年龄等于18）
```terminal
curl -XGET "http://elasticsearch:9200/table1/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "match": {
      "age": 18
    }
  }
}'
```

### 模糊匹配搜索（name含有伟所有的数据）
```terminal
curl -XGET "http://elasticsearch:9200/table1/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "match": {
      "name": "伟"
    }
  }
}'
```

### 多个关键字模糊匹配 (name含有[伟、大]所有的数据）
```terminal
curl -XGET "http://elasticsearch:9200/table1/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "match": {
      "name": "伟 大"
    }
  }
}'
```

### 短语关键字模糊匹配 (name含有[伟 大]所有的数据）
> match_phrase是短语匹配
```terminal
curl -XGET "http://elasticsearch:9200/table1/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "match_phrase": {
      "name": "伟 大"
    }
  }
}'
```

### bool过滤查询，可以做组合过滤查询、嵌套查询等
```json
{
    "bool" : {
        "filter":   [],
        "must" :    [],
        "should":   [],
        "must_not": []
    }
}
```

###### 说明：
- `filter`：过滤
- `must`：条件必须满足，相当于and
- `should`：条件可以满足也可以不满足，相当于or
- `must_not`：条件不需要满足，相当于not


### filter指定单个值(term)
```terminal
# 相当于MySQL的 select * from table1 where age = 18;
curl -XGET "http://elasticsearch:9200/table1/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "bool": {
      "filter": {
        "term": {
          "age": 18
        }
      }
    }
  }
}'
```

### filter指定多个值(terms)
```terminal
# 相当于MySQL的 select * from table1 where age in (18,19);
curl -XGET "http://elasticsearch:9200/table1/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "bool": {
      "filter": {
        "terms": {
          "age": [18, 19]
        }
      }
    }
  }
}'
```

### must、should、must_not与term结合使用
```terminal
# 相当于MySQL的 select * from table1 where age = 18 or age = 19 and sex != false;
curl -XGET "http://elasticsearch:9200/table1/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "bool": {
      "should": [
        {
          "term": {
            "age": 18
          }
        },
        {
          "term": {
            "age": 19
          }
        }
      ],
      "must_not": {
        "term": {
          "sex": false
        }
      }
    }
  }
}'
```

### 范围查找

- `gt`:  > 大于
- `lt`:  < 小于
- `gte` :  >= 大于等于
- `lte` :  <= 小于等于

```terminal
# 相当于MySQL的 select * from table1 where age >= 10 and age <= 20;
curl -XGET "http://elasticsearch:9200/table1/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "bool": {
      "filter": {
        "range": {
          "age": {
            "gte": 10,
            "lte": 20
          }
        }
      }
    }
  }
}'
```

### group

```terminal
# 相当于MySQL的 select age,count(*) from table1 group by age;
curl -XGET "http://elasticsearch:9200/table1/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "aggs": {
    "group_by_age": {
      "terms": {
        "field": "age"
      }
    }
  }
}'
```

!> elasticsearch 进行排序的时候，我们一般都会排序数字、日期，而文本排序则会报错

聚合这些操作，用单独的数据结构(fielddata)缓存到内存里了，需要单独开启，官方解释在此fielddata
```terminal
curl -XPOST "http://elasticsearch:9200/table1/_mapping" -H 'Content-Type: application/json' -d'
{
  "properties": {
    "name": {
      "type": "text",
      "fielddata": true
    }
  }
}'
```
> `table1`: 索引库名称  `name`: 需要开启的字段

### 安装名字group by，然后计算平均年龄
```terminal
# 相当于MySQL的 select name,avg(age) from table1 group by name;
curl -XGET "http://elasticsearch:9200/table1/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "size": 0,
  "aggs": {
    "group_by_state": {
      "terms": {
        "field": "name"
      },
      "aggs": {
        "average_age": {
          "avg": {
            "field": "age"
          }
        }
      }
    }
  }
}'
```

