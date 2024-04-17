本章介绍MongoDB 地理位置查询-根据几何形状查询，MongoDB支持图形交集、包含关系查询。

- 应用场景：
    - 在地图上画一个圈，查询这个圈内的数据。

## 前置教程
<a href='/#/数据库/mongodb/position/model.md'>MongoDB地理位置存储格式</a>

- 根据距离查询数据的前置条件如下：
    - 每个文档数据中有保存坐标数据的字段，例如: location字段保存店铺坐标。
    - 创建2dsphere 或者 2d 空间索引。

## $geoWithin 操作符

通常用于查询包含在指定图形范围内的坐标点，例如：画图找房，在地图上画一块区域，查询这块区域内的房源。

语法格式：
```terminal
{
   <location field>: { // 查询字段
      $geoWithin: {
         $geometry: {
            type:  , // 仅支持Polygon多边形，或者多多变形。
            coordinates: [ <coordinates> ]  // 图形坐标点集合
         }
      }
   }
}
```

例子：
```terminal
db.places.find(
   {
     loc: { // loc字段保存坐标数据
       $geoWithin: {
          $geometry: {
             type : "Polygon" ,
             coordinates: [ [ [ 0, 0 ], [ 3, 6 ], [ 6, 1 ], [ 0, 0 ] ] ] // 查找在这个图形区域内的数据
          }
       }
     }
   }
)
```
查询places集合中，在指定图形区域内的文档数据。

## $geoIntersects 操作符

跟$geoWithin的区别是，$geoIntersects是判断两个图形之间是否有交集。

格式：
```terminal
{
  <location field>: { // 保存图形坐标点的字段
     $geoIntersects: {
        $geometry: {
           type: "<geojson object type>" , // 图形类型
           coordinates: [ <coordinates> ] // 图形坐标点集合, 查询跟这个图形有交集的数据
        }
     }
  }
}
```

例子：
```terminal
db.places.find(
   {
     loc: { // loc字段保存区域数据，就是一张几何图形
       $geoIntersects: {
          $geometry: {
             type: "Polygon" ,
             coordinates: [
               [ [ 0, 0 ], [ 3, 6 ], [ 6, 1 ], [ 0, 0 ] ] // 查询跟这个图形有交集的数据
             ]
          }
       }
     }
   }
)
```