循环控制语句

循环控制语句可以控制循环体内语句的执行过程。

GO 语言支持以下几种循环控制语句：

### Goto、Break、Continue

- 1.三个语句都可以配合标签(label)使用
- 2.标签名区分大小写，使用不当会造成编译错误
- 3.continue、break配合标签(label)可用于多层循环跳出
- 4.goto是调整执行位置，与continue、break配合标签(label)的结果并不相同  