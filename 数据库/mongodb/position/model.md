本章介绍MongoDB地理空间数据如何存储，我们要想使用MongoDB地理信息查询功能，首先得了解如何存储地理空间数据。

地理空间数据，主要由点、线、几何图形组成。

在地图上的一个点，是有经纬度坐标的，多个点就连成线，多条线可以组成各种形状的图形。

在实际应用场景中：
店铺的位置、我在哪里？、学校在哪里，都可以使用坐标点表示。如果要描述校园的范围、商场的范围就需要几何图形描述。

## GeoJSON 对象
MongoDB通过GeoJSON对象表达地理信息数据。

- GeoJSON常用类型如下：
    - Point - 坐标点
    - LineString - 线条
    - Polygon - 多边形

MongoDB字段保存GeoJSON对象数据格式如下：
```terminal
    <field>: { type: <geojson type> , coordinates: <coordinates> }
```

- 说明：
    - `<field>` - 字段名
    - `type` - GeoJSON类型
    - `coordinates` - 坐标点数组，不同的GeoJSON类型格式不一样。

例子：
```terminal
    location: {
        type: "Point",  // 地理信息数据类型，这里是坐标点
        coordinates: [-73.856077, 40.848447] // 经度,纬度
    }
```

说明：location字段，保存了一个坐标点类型的地理空间数据

### 坐标点（Point）

格式:
```terminal
    { type: "Point", coordinates: [ 经度, 纬度 ] }
```

例子:
```terminal
    { type: "Point", coordinates: [ 40, 5 ] }
```

### 线 （LineString）

格式：
```terminal
    { type: "LineString", coordinates: [ 坐标点1, 坐标点2, ...] }
```

例子:
```terminal
    { type: "LineString", coordinates: [ [ 40, 5 ], [ 41, 6 ] ] }
```

### 多边形 （Polygon）

可以由一条或者多条线组成。

格式：
```terminal
{
  type: "Polygon",
  coordinates: [ 
        线段1,
        线段2，
        ....
    ]
}
```

?>说明：每条线段开始坐标和结束坐标，必须一样，才能形成闭合的图形。

### 一条线段组成的图形
```terminal
{
  type: "Polygon",
  coordinates: [
          [ [ 0 , 0 ] , [ 3 , 6 ] , [ 6 , 1 ] , [ 0 , 0  ] ] // 线段坐标，注意第一个和最后一个坐标，是一样的。
    ]
}
```

### 多条线段组成的图形
```terminal
{
  type : "Polygon",
  coordinates : [
     [ [ 0 , 0 ] , [ 3 , 6 ] , [ 6 , 1 ] , [ 0 , 0 ] ], // 线段1
     [ [ 2 , 2 ] , [ 3 , 3 ] , [ 4 , 2 ] , [ 2 , 2 ] ]  // 线段2
  ]
}
```

![img](./img/1.png ':width=80%')

## 地理位置索引

MongoDB支持两种地理位置索引，用于加快GeoJSON类型数据的查询速度。

### 2dsphere

这是一种球面几何位置的索引类型，就是计算两点间距离的时候，2dsphere会考虑地球是圆的。

创建2dsphere索引例子:
```terminal
    db.collection.createIndex( { location : "2dsphere" } )
```
为location字段创建索引。

### 2d
是一种平面几何类型，计算两点间距离，就当成平面计算。

创建2d索引例子:
```terminal
    db.collection.createIndex( { location : "2d" } )
```
为location字段创建索引。

## 地理信息查询类型
- 地理信息查询，都是跟几何计算有关，下面是MongoDB支持的查询类型

    - `$geoIntersects` - 用于匹配跟指定图形有交集的文档数据
    - `$geoWithin` - 用匹配包含在指定图形区域内的文档数据
    - `$near` - 通常用于查询距离指定坐标点，最近的文档数据

?>提示：地理信息查询，请参考后续章节。