本章介绍MongoDB地理位置查询功能之一，根据距离查询文档数据。

应用场景：查询附近店铺、附近的人。

## 前置教程
<a href='/#/数据库/mongodb/position/model.md'>MongoDB地理位置存储格式</a>

- 根据距离查询数据的前置条件如下：
    - 每个文档数据中有保存坐标数据的字段，例如: location字段保存店铺坐标。
    - 创建2dsphere 或者 2d 空间索引。

## $near操作符

MongoDB通过$near操作符实现根据距离查询文档数据。

格式：
```terminal
{
   <location field>: { // 保存坐标数据的字段
     $near: {
       $geometry: { // 设置作为对比的基准坐标
          type: "Point" ,
          coordinates: [ 经度 , 纬度 ]
       },
       $maxDistance: 最大距离，单位米,
       $minDistance: 最小距离，单位米
     }
   }
}
```

?>提示：$near查询返回的数据，按距离由近到远排序。

## 例子

假如shop集合保存了店铺数据，location字段保存每一个店铺的坐标，下面查询距离我最近的店铺（最小距离1000米，最大距离5000米）。
```terminal
db.shop.find(
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
)
```