本章介绍Golang MongoDB地理信息查询, 包括如何保存位置信息、按距离查询。

## 前置教程
- <a href='/#/数据库/mongodb/README'>MongoDB教程</a>
- <a href='/#/数据库/mongodb/position/model'>MongoDB地理信息查询</a>
- <a href='/#/编程语言/golang/MongoDB/fast_induction'>Golang MongoDB快速入门</a>

?>提示：不了解MongoDB查询语法，请先阅读MongoDB教程，Golang操作MongoDB使用的也是一样的表达式语法。

## 准备测试数据

往coll集合写入一些包含地理坐标的数据，location字段存储了店铺的经纬度坐标地址。
```terminal
docs := []interface{}{
    bson.D{
        {"title", "海南白切鸡"},
        {"location", bson.D{
            {"type": "Point"},
            {"coordinates": bson.A{116.240015,39.899617}}
        }}
    },
    bson.D{
        {"title", "广东烧腊"},
        {"location", bson.D{
            {"type": "Point"},
            {"coordinates": bson.A{116.268854,39.900276}}
        }}
    },
    bson.D{
        {"title", "广东烧鹅"},
        {"location", bson.D{
            {"type": "Point"},
            {"coordinates": bson.A{116.264905,39.902778}}
        }}
    },
    bson.D{
        {"title", "山西大饼"},
        {"location", bson.D{
            {"type": "Point"},
            {"coordinates": bson.A{116.288938,39.893164}}
        }}
    },
    bson.D{
        {"title", "杭州小笼包"},
        {"location", bson.D{
            {"type": "Point"},
            {"coordinates": bson.A{116.286191,39.910415}}
        }}
    }
}

result, err := coll.InsertMany(context.Background(), docs)
```

## 按距离查询
使用$near操作符实现按距离查询。

假如coll集合保存了店铺数据，location字段保存每一个店铺的坐标，下面查询距离我最近的店铺（最小距离1000米，最大距离5000米）。
```terminal
cursor, err := coll.Find(
    context.Background(),
    bson.D{{"location", bson.D{
        {"$near": bson.D{
            {"$geometry": bson.D{{"type":"Point"}, {"coordinates": bson.A{116.288938,39.893164}}}}, // 我的当前坐标
            {"$minDistance": 1000}, // 最小距离
            {"$maxDistance": 5000} // 最大距离
        }}
    }}}
)
```

等价表达式：
```terminal
{
     location:
       { $near :
          {
            $geometry: { type: "Point",  coordinates: [ -73.9667, 40.78 ] }, // 我的坐标
            $minDistance: 1000, // 最小距离
            $maxDistance: 5000 // 最大距离
          }
       }
}
```
跟mongodb原生表达式一样，区别只是用Golang的数据结构重新描述一遍。