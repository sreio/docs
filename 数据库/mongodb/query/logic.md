本章介绍，MongoDB的逻辑操作符，类似SQL的and、or条件。

## MongoDB支持的逻辑操作符
|         操作符         |       描述        
| :-----------: | :------------------------------: | 
|      $and      |  类似SQL的and条件，两个条件都匹配
|      $not      |  条件取反
|      $or       |  类似SQL的or条件，匹配其中一个条件   
|      $nor      |  两个条件都不匹配 

## $and

语法：
```terminal
    { $and: [ { <expression1> }, { <expression2> } , ... , { <expressionN> } ] }
```
- 说明:
    - `<expression>` 子表达式
    - $and接收一个数组，包含一组子表达式，必须匹配所有子表达式

例子：
```terminal
    db.inventory.find( { $and: [ { qty: { $lt : 10 } }, { qty : { $gt: 50 } }] } )
```

等价SQL：
```terminal
    select * from inventory where qty > 50 and qty < 10
```

## $not
```terminal
    db.inventory.find( { price: { $not: { $gt: 1.99 } } } )
```
- 说明：
    - 条件大于1.99，条件取反就是小于等于1.99
    - 含义是查询price<=1.99 或者 price字段值不存在的文档。

类似SQL：
```terminal
    select * from inventory where price <= 1.99
```

## $or

类似SQL的or条件

语法：
```terminal
    { $or: [ { <expression1> }, { <expression2> }, ... , { <expressionN> } ] }
```

- 说明:
    - `<expression>` 子表达式
    - $or接收一个数组，包含一组子表达式, 只要匹配其中一个表达式即可

例子：
```terminal
    db.inventory.find( { $or: [ { quantity: { $lt: 20 } }, { price: 10 } ] } )
```

等价SQL：
```terminal
    select * from inventory where quantity < 20 or price=10
```

## $nor

逻辑NOR计算

语法:
```terminal
    { $nor: [ { <expression1> }, { <expression2> }, ...  { <expressionN> } ] }
```

例子:
```terminal
    db.inventory.find( { $nor: [ { price: 1.99 }, { sale: true } ]  } )
```

- 含义说明：
    - price != 1.99 且 sale != true 或者
    - price != 1.99 且 不包含sale字段 或者
    - 不包含price字段，且 sale != true 或者
    - 即不包含price字段也不包含sale字段