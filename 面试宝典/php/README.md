?> 复习整理PHP相关的学习资料


## isset和empty的区别？

`Isset`测试变量是否`被赋值`，如果这个变量没被赋值，则返回false，`empty`是判断变量是否`为空`，当赋值为0，null,’’,返回true为真。他们之间最大的区别就是当一个变量被赋值0时，empty判断它为空，而isset判断它有值不为空。

## php基本变量类型

- `四种标量类型` ：boolean （布尔型）、integer （整型）、float （浮点型, 也称作 double)、string （字符串）
- `四种复合类型` ：array （数组）、object （对象）、callable、iterable
- `最后是两种特殊类型` ：resource（资源）、NULL（NULL）

## PHP魔术方法

13个常用的魔术方法:  `__construct、` `__destruct、` `__call` 或 `__classStatic、` `__get、` `__set、` `__isset、` `__unset、` `__toString、` `__clone、` `__sutoload、` `__invoke、` `__sleep、` `__wakeup`

1. `__construct 构造方法`: 当一个类被实例化创建对象时，会首先执行构造方法。
2. `__destruct 析构方法`: 当对象在销毁之前或失去对对象的引用时，会调用 析构方法。
3. `__call 或 __callStatic` :当调用一个未定义的或没有权限的成员方法时，会调用 __call 方法。（当在静态方法中调用一个未定义的或没有权限的成员方法时，则会调用 __callStatic 方法。）如果本类找不到调用的成员方法，会去父类中找。如果本类找不到 __call 方法，会去父类中找。
4. `__get`:当调用一个未定义的或非公有的成员属性时，会调用 __get 方法。
5. `__set`: 当给一个未定义的或非公有的成员属性赋值时， 会调用 __set 方法。
6. `__isset`:当在一个未定义的或非公有的成员属性上调用 isset函数时，会调用 __isset 方法。
7. `__unset`:当在一个未定义或非公有的成员属性上调用 unset函数时，会调用 __unset 方法。
8. `__toString`:在打印输出一个对象时, 会自动调用 __toString 方法。 例: echo 对象名。
9. `__clone`:当克隆一个对象时, 会自动调用 __clone 方法。 例: $clone_obj = clone 对象名;
10. `__autoload`:在实例化一个尚未被定义的类时会自动调用 __autoload 来加载类文件。
11. `__invoke`:当尝试以调用函数的方式调用一个对象时, 会自动调用 __invoke 方法。
12. `__sleep`:serialize() 函数会检查类中是否存在 __sleep 方法，如果存在，先执行 __sleep 方法，再执行 序列化操作。
```php
    class User
    {
        public function __sleep(){
            // 
        }
    }
    $obj = new User();
    serialize($obj);
```

13.  `__wakeup`:unserialize() 函数会检查类中是否存在 __wakeup 方法，如果存在，先执行 __wakeup 方法，再执行 反序列化操作。  
```php
class User
{
    public function __wakeup(){
        // 
    }
}
$obj = new User();
unserialize($obj);
```

## PHP魔术常量

PHP 含有 9 个魔术常量。它们的值随着它们在代码中的位置改变而改变。

|名称 |              说明 |
|:---:|:---:|
|__LINE__         |  文件中的当前行号
|__FILE__         |  文件的完整路径和文件名。如果用在被包含文件中，则返回被包含的文件名。
|__DIR__          |  文件所在的目录。如果用在被包括文件中，则返回被包括的文件所在的目录。它等价于 dirname(__FILE__)。除非是根目录，否则目录中名不包括末尾的斜杠。
|__NAMESPACE__    |  当前命名空间的名称。
|__TRAIT__        |  Trait 的名字。Trait 名包括其被声明的作用域（例如 Foo\Bar）。
|__CLASS__        |  当前类的名称。类名包括其被声明的作用域（例如 Foo\Bar）。当用在 trait 方法中时，CLASS 是调用 trait 方法的类的名字。
|__FUNCTION__     |  当前函数的名称。匿名函数则为 {closure}。
|__METHOD__       |  类的方法名。
|ClassName::class |  完整的类名。

## PHP超全局变量

PHP 有 9 个超全局变量:`$_SERVER`、`$_GET`、`$_POST`、`$_REQUEST`、`$_COOKIE`、`$_SESSION`、`$_FILES`、`$_ENV`、`$GLOBALS`

- `$_SERVER`
  - $_SERVER: 一个包含了诸如头信息（header）、路径（path）、以及脚本位置（srcipt location）等信息的数组。这个数组中的项目由Web服务器创建。
    > **常用 $_SERVER 中的参数**
    > |参数 |	                        描述|
    > |:---:|:---:|
    > |$_SERVER['SERVER_NAME']    |      当前运行脚本所在服务器主机的名称
    > |$_SERVER['REQUEST_METHOD'] |      访问页面时的请求方法。例如：GET、HEAD，POST，PUT
    > |$_SERVER['QUERY_STRING']	  |   查询(query)的字符串。例如: www.bqhub.com?a=1 。 则 获取到的值为 “a=1”
    > |$_SERVER['REQUEST_URI']    |      访问此页面所需的URI。例如: www.bqhub.com?a=1 。 则 获取到的值为 “/?a=1”
    > |$_SERVER['SCRIPT_NAME']    |      包含当前脚本的路径。 例如: index.php
    > |$_SERVER['PHP_SELF']       |      当前正在执行的脚本文件名。
    > |$_SERVER['REMOTE_ADDR']    |      当前页面用户的IP地址。
    > |$_SERVER['REMOTE_HOST']    |      当前页面用户的主机名。
- `$_GET` 可以获取到使用 get 方法传递的参数的相关信息。
- `$_POST` 可以获取到使用 post 方法传递的参数的相关信息。
- `$_REQUEST` 是一个关联数组，默认包含 $_GET、$_POST、$_COOKIE 中的内容。建议不用这个超级变量，因为它不够安全。
- `$_COOKIE` 是一个关联数组，包含 通过 HTTP cookie 传递给当前脚本的内容。
- `$_SESSION` 是一个关联数组，包含当前脚本中的所有 session 内容。
- `$_FILES` 是一个关联数组，包含通过 HTTP POST 方法上传给当前脚本的文件内容。
- `$_ENV` 是一个包含服务器端环境变量的数组。
- `$GLOBALS` 是一个关联数组， 包含对当前脚本全局 范围内定义的所有变量。

## PHP垃圾回收机制（GC）

- 1. 使用 引用计数机制
- 2. 将每个 PHP 变量保存在一个叫 zval 变量容器中。
- 3. zval 变量容器 包含 变量的类型、变量值、 is_res、refcount
- 4. `is_ref` 用于标识该变量是否为引用集合或变量。
- 5. refcount 表示指向当前变量的个数。
- 6. 默认打开垃圾回收机制, 当发现有存在循环引用的zval时, 就会把其投入到根缓冲区, 当根缓冲区达到配置文件中的指定数量后, 就会进行垃圾回收, 以此解决8. 循环引用导致的内存泄露问题
- 7. 如果引用计数减少到零, 所在变量容器将被清除（free）, 不属于垃圾；
- 8. 如果一个zval的引用计数减少后还大于0, 那么它会进入垃圾周期。
- 9. 其次, 在一个垃圾周期中, 通过检查引用计数是否减1, 并且检查哪些变量容器的引用次数是零, 来发现哪部分是垃圾。

## 一、 面向对象基本知识
### 1.1 什么是类
 - 类是事物相关属性特征和行为特征的集合。

   - 属性特征: 就是该事物的状态。比如： 用户性别、用户的身高。

   - 行为特征: 就是该事物能够做什么。比如： 用户下单、用户评论。

 - ⚠️ 注意

   - 属性特征在类中被称为： 成员属性或成员变量

   - 行为特征在类中被称为： 成员方法

### 1.2 什么是对象
 - 对象是客户存在的一个实例，是事物的具体实现。可以通过对象调用类的属性 与 行为。

### 1.3 类与对象的关系
 - 类是对对象抽象的一个描述
 - 对象是客观存在的一个实体

### 1.4 PHP创建类的示例
```php
<?php
    # 类: 创建名为 Car 的类
    public class Car
	{
    	# 成员属性
    	public $default_config;
    	static $default_name;
    
    	# 成员方法
    	public function get_config()
        {
            // 需要完成的功能
        }
    	static function get_name()
        {
            // 需要完成的功能
        }
	}

	# 对象: $car_obj 就是 Car 类的一个实例
	# 对象 = new 类名();
	$car_obj = new Car();


	# static 静态修饰符访问示例
        # 格式: 类名::属性名 , 示例: 
        $default_name = Car::default_name;

        # 格式: 类名::方法名 , 示例: 
        $name = Car::get_name(); 

        # 格式: $对象名->静态属性名 , 示例:
        $default_name = $car_obj->default_name;

        # 格式: $对象名->静态方法名 , 示例:
        $name =$car_obj->get_name();
```

## 二、 类、属性、方法的修饰符
### 2.1 类的修饰符
 - 类的修饰符有: `abstract`、`final`、`interface`、`trait`

### 2.2 成员方法的修饰符
 - 成员方法的修饰符有: `public`、`protected`、`private`、`static`、`abstract`、`final`

### 2.3 成员属性修饰符
 - 属性修饰符有: public、protected、private、static、var
 - var 与 public 作用相同, var 是 public 的别名。

### 2.4 static 静态修饰符
示例请看上边`1.5`的代码

 - `static` 静态修饰符 : 用于修饰类的成员属性和成员方法 。

 - `static` 关键字 : 修饰的成员方法称为静态方法，修饰的成员属性称为静态属性。


调用方法:

  1. 可以不用 new（实例化）就可以直接调用, 格式 类名::属性名
  2. 静态方法在实例化后的对象也可以访问, 格式 对象名->属性名

⚠️注意:

  1. 在静态方法中不可以使用非静态的内容。就是不让使用 $this
  2. 在类的方法中可以使用其他静态属性和静态方法，不过要使用self关键字，如 self::静态属性名 或 self::静态方法名

### 2.5 final 修饰符
 - 类使用时: 如果类使用 final关键字修饰时 ，表示这个类不可以有子类，即不能被 继承。

 - 成员方法使用时: 如果成员方法 使用 final 关键字修饰时，表示这个成员方法不可以在子类中被覆盖，即不能被 重写。

### 2.6 abstract 抽象修饰符
类使用时:

```php
<?php
	abstract class ClassName{
		public function functionName();
	}
```
1. 抽象类中的成员方法没有方法体，以 (); 结束。
2. 该类不能被实例化
3. 若想使用抽象类，就必须定义一个类去继承这个抽象类，并定义覆盖父类的抽象方法(实现抽象方法)。

### 2.7 interface 接口修饰符
 - 假如一个抽象类中所有的方法都是抽象的，那么我们可以使用另外一种方式定义：接口。
 - 接口使用关键字interface来定义，接口中只能有常量与抽象方法。
 ```php
 <?php
	# 接口定义格式：
	interface interfaceName{
		# 常量定义
		const USERLEVEL = 'TOP';

		# 抽象方法定义, 注意抽象方法不需要有 abstract 关键字, 且以 (); 结尾
		function funName();
	} 
```
 ```php
<?php
	# 接口实现, 定义一个类, 使用 implements 关键字实现
	class 类名 implements 接口名1, 接口名2
	{
		# 必须将 接口 中的所有方法全部重写实现
	}
```

## 三、 面向对象三大特性
 - 三大特性: 封装、继承、多态。

### 3.1 封装
 - 我理解的 封装 就是 类的定义, 将事物相关属性特征和行为特征的集合在一起，形成一个 类，这就是封装。

### 3.2 继承
 - 继承 顾名思义就是 B类 继承 A类，继承后， B类 就可以调用访问 A类 非私有的成员属性与成员方法。通过继承创建的类被称为 “子类” 或 “派生类”。被继承的类称为 “基类” 或 “父类”。


⚠️ PHP 通过 extends 关键字继承，且一个类只能继承一个父类。

```php
<?php

    # 父类
    class A{

	}

	# 子类
	class B extends A{

    }
```

### 3.3 多态
 - 我理解的多态就是: 同一个方法，传入不同的对象，实现不同的效果。

```php
<?php

# 注意: 该部分代码没有实际运行, 不保证运行结果可以成功。
# 但是这个逻辑。

	class BuyCar{
		function buyFunc($obj){
         if ($obj instanceof Car){
             $obj->buy();
         } else {
             echo 'No Buy';
         }
     }
	}

	/**
	 * 定义 Car 接口
	 */
	interface Car{
        function buy();
    }

	/**
	 * 定义奔驰类
	 */
	class Benz implements Car{
        function buy(){
            echo 'Benz buy';
        }
    }

	/**
	 * 定义宝马类
	 */
	class Bmw implements Car{
        function buy(){
            echo 'Bmw buy';
        }
    }

	# 实例化 BuyCar 类
	$BuyCar_obj = new BuyCar();

	# 调用 buyFunc
	$BuyCar_obj->buyFunc(new Benz());

	$BuyCar_obj->buyFunc(new Bmw());

```

## 四、 面向对象的七大原则
 - 单一职责原则、开放封闭原则、里式替换原则、依赖倒置原则、接口隔离原则、迪米特原则、合成/聚合复用原则

### 4.1 单一职责原则
 - 单一职责原则(Single Responsibility Principle，SRP): 一个类应仅有一个职责。

 - 通俗的讲就是一个类当中的所有成员方法完成的工作相关性是相同的。

 - 例: 一个类不能即完成用户的注册, 又完成商品的购买。

### 4.2 开放封闭原则
 - 开放封装原则(Open-Closed Principle，OCP): 即对扩展开放对修改关闭。

 - 通俗的讲就是 在不修改源代码的情况下，对其扩展，不影响原有功能。

### 4.3 里式替换原则
 - 里式替换原则(Liskov Substitution Principle ，LSP): 所有引用基类的地方必须能透明地使用其子类的对象。

 - 通俗的讲就是 子类可扩展父类, 而不是覆盖父类或改变父类原有的功能。

### 4.4 依赖倒置原则
 - 依赖倒置原则(Dependency Inversion Principle ，DIP): 依赖于抽象。

 - 通俗的讲:

    1. 高层次模块不应该依赖于低层次模块，两者都应依赖于抽象
    2. 抽象不应该依赖细节，细节应该依赖于抽象。

### 4.5 接口隔离原则
 - 接口隔离原则(Interface Segregation Principle, ISP): 使用多个小且专门的接口, 不要使用一个大的总接口。

 - 通俗的讲就是: 不要把所有功能都写在一个接口中，干啥的就是干啥的，且不应该依赖那些不需要用不着的接口。

### 4.6 迪米特原则
 - 迪米特原则(Law of Demeter ，LoD): 降低类与类之间的耦合。

 - 通俗的讲就是: 没有关系的类别硬往一起扯，减少没用的交际。

### 4.7 合成/聚合复用原则
 - 合成/聚合复用原则(Composite/Aggregate Reuse Principle ，CARP): 尽量使用对象组合，而不是继承来达到复用的目的。

 - 通俗的讲就是: PHP类之间的关系，尽可能的多使用 trait和 use ，少使用 extends。
