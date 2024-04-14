> 问题： 不用额外的变量，交换两个变量的值


### golang实现方案
 
```go
a, b := 1, 2
a, b = b, a
```

## 方法一：数组交换

<!-- tabs:start -->

#### **js**

```js
let a = 1
let b = 2
[ a, b ] = [ b, a ]

console.log('a', a, 'b', b)
```

#### **php**

```php
list($a, $b) = array($b, $a);

# 或 数组下标
$array[0] = $a;
$array[1] = $b;
$a = $array[1];
$b = $array[0];
```

<!-- tabs:end -->

## 方法二：异或

<!-- tabs:start -->

#### **js**

```js
let a = 1
let b = 2
a = a ^ b
b = a ^ b
a = a ^ b
console.log('a', a, 'b', b)
```

#### **php**

```php
$a=1;
$b=2;
$a=$a^$b;
$b=$a^$b;
$a=$a^$b;
```
<!-- tabs:end -->

### 讲解

> a^b：异或运算符，如果两个数相同，结果为0，否则为1
>
> `a=`: 1的二进制为 0001 `b=2`：2的二进制为 0010 

1. 第一次`a^b`,也就是 `1^2`
   > 1：0001
   >
   > 2：0010
   >
   > 结果：0011 ,换算为十进制就是3
2. 第二次`a^b`,也就是 `3^2`
   > 3：0011
   >
   > 2：0010
   >
   > 结果：0001 ,换算为十进制就是1
3. 第三次`a^b`,也就是 `3^1`
   > 3：0011
   >
   > 1：0001
   >
   > 结果：0010 ,换算为十进制就是2

 

## 方法三：使用加法交换两个变量的值

<!-- tabs:start -->
#### **js**

```js
let a = 1
let b = 2

a = a + b // The value of a is a(1) + b(2) = 3
b = a - b // The value of b is a(3) - b(2) = 1
a = a - b // The value of a is a(3) - b(1) = 2

console.log('a', a, 'b', b)

```

#### **php**

```php
$a=1;
$b=2;
$a=$a+$b;
$b=$a-$b;
$a=$a-$b;
```
<!-- tabs:end -->

## 方法四：使用减法交换两个变量的值
<!-- tabs:start -->
#### **js**
```js
let a = 1
let b = 2

a = a - b // The value of a is a(1) - b(2) = -1
b = b + a // The value of b is b(2) + a(-1) = 1
a = b - a // The value of a is b(1) - a(-1) = 2

console.log('a', a, 'b', b)
```
#### **php**
```php
$a=1;
$b=2;
$a=$a-$b;
$b=$b+$a;
$a=$b-$a;
```
<!-- tabs:end -->
