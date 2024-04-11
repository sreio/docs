## 索引库
```bash
# 创建索引库
curl -XPUT "http://elasticsearch:9200/table1" -H 'Content-Type: application/json' -d'
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
        "type": "date"
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
}'

# 追加索引库字段
curl -XPUT "http://elasticsearch:9200/table1/_mapping" -H 'Content-Type: application/json' -d'
{
  "properties": {
    "update_date": {
      "type": "date"
    }
  }
}'

# 获取索引库
curl -XGET "http://elasticsearch:9200/table1"

# 删除索引库
curl -XDELETE "http://elasticsearch:9200/table1"
```

## 文档
```bash
# 创建文档
curl -XPOST "http://elasticsearch:9200/table1/_doc/1" -H 'Content-Type: application/json' -d'
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
}'

# 查询文档
curl -XGET "http://elasticsearch:9200/table1/_doc/1"

# 增量修改文档
curl -XPOST "http://elasticsearch:9200/table1/_update/1" -H 'Content-Type: application/json' -d'
{
  "doc": {
    "age": 17,
    "update_date": "2021-04-09 13:52:01"
  }
}'

# 全量修改文档
curl -XPUT "http://elasticsearch:9200/table1/_doc/1" -H 'Content-Type: application/json' -d'
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
}'

# 删除文档
curl -XDELETE "http://elasticsearch:9200/table1/_doc/1"
```