?> 复习整理PHP相关的学习资料

## 基础篇

### php基本变量类型

- `四种标量类型` ：boolean （布尔型）、integer （整型）、float （浮点型, 也称作 double)、string （字符串）
- `四种复合类型` ：array （数组）、object （对象）、callable、iterable
- `最后是两种特殊类型` ：resource（资源）、NULL（NULL）

### PHP魔术方法

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

### PHP魔术常量

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

### PHP超全局变量
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


### PHP 错误级别介绍与设置错误级别的方式
#### 常见错误级别有：
 - `E_ERROR` : 致命的运行错误并阻止脚本执行
 - `E_WARNING` : 运行时警告
 - `E_PARSE` : 解析错误
 - `E_NOTICE` : 注意
 - `E_USER_ERROR` : 用户生成的错误消息
 - `E_USER_WARNING` : 用户生成的警告
 - `E_USER_NOTICE` : 用户生成的注意
 - `E_ALL` : 所有的错误、警告、注意

#### 设置错误级别的方式:
 - 修改 php.ini 配置文件
    - 例: error_reporting = E_ALL & ~E_NOTICE, 表示报告除 E_NOTICE 之外的所有错误。
 - error_reporting 函数设置
    - 例: error_reporting(E_ERROR | E_WARNING);

### PHP异常处理
```php
<?php
   # php 使用 try catch 来捕获异常
   # 例: 
   try
   {
       if ($count > 10) throw new Exception('数量不可超过 10 个');
       if ($width > 100) throw new widthException('宽度不可超过 100 米');
       if ($height > 150) throw new heightException('高度不可超过 150 米');
   } catch (Exception $e){
       # 常用异常捕获信息
       echo $e->getLine();
       echo $e->getCode();
       echo $e->getFile();
       echo $e->getMessage();
   } catch (heightException $e){
       echo $e->getMessage();
   } catch (widthException $e){
       echo $e->getMessage();
   } 

```

## 进阶篇

### PHP垃圾回收机制（GC）

- 1. 使用 引用计数机制
- 2. 将每个 PHP 变量保存在一个叫 zval 变量容器中。
- 3. zval 变量容器 包含 变量的类型、变量值、 is_res、refcount
- 4. `is_ref` 用于标识该变量是否为引用集合或变量。
- 5. refcount 表示指向当前变量的个数。
- 6. 默认打开垃圾回收机制, 当发现有存在循环引用的zval时, 就会把其投入到根缓冲区, 当根缓冲区达到配置文件中的指定数量后, 就会进行垃圾回收, 以此解决8. 循环引用导致的内存泄露问题
- 7. 如果引用计数减少到零, 所在变量容器将被清除（free）, 不属于垃圾；
- 8. 如果一个zval的引用计数减少后还大于0, 那么它会进入垃圾周期。
- 9. 其次, 在一个垃圾周期中, 通过检查引用计数是否减1, 并且检查哪些变量容器的引用次数是零, 来发现哪部分是垃圾。

### PHP底层原理

#### PHP代码执行过程：

 1. 启动 php 及 zend 引擎
 2. 加载注册拓展模块
 3. 对代码进行词法/语法分析
 4. 编译成opcode(opcache)
 5. 执行 opcode

#### PHP 的四层体系, 从下至上分为四层:

 - Zend 引擎
   - Zend 引擎整体用C语言实现，是 PHP 的内核部分，它负责将 PHP 代码翻译（词法、语法解析等一系列编译过程）为可执行的 opcode 操作码，并实现相应的处理方法、基本的数据结构（如 hashtable、OO）、内存分配及管理、提供相应的 API 方法供外部调用。
 - 扩展层
   - 围绕着 Zend 引擎，Extensions 通过组件化的方式提供各种基础服务，我们常见的各种内置函数（例如变量操作函数、字符串操作函数等）以及标准库等都是通过 Extensions 来实现。
 - SAPI（服务器应用程序编程接口）
   - SAPI 通过一系列钩子函数，使得 PHP 可以和外围交互数据，这是 PHP 非常优雅和成功的一个设计，通过 SAPI 成功的将 PHP 本身和上层应用解耦隔离，PHP 可以不再考虑如何针对不同应用进行兼容，而应用本身也可以针对自己的特点实现不同的处理方式。
 - Application（上层应用）

  ![img](./img/dc.png ':size=50%')

### PHP运行模式以及各自的原理

#### 先了解一下 CGI :

 - CGI（Common Gateway Interface）全称是“通用网关接口”，是一种让 客户端 与 Web服务器 程序进行通信（数据传输）的协议。
 - CGI 用来规范 Web服务器 传输到 解析器(例: php-cgi) 中的数据类型以及数据格式，包括URL、查询字符串、POST数据、HTTP header等。
 - 解析器只要符合 CGI 标准，就能作为一个 cgi 程序与 Web 服务器交互。
 - 一次请求都要 fork 一个进程, 然后销毁，也就是（fork-and-execute）模式，性能较低。

##### PHP 运行模式:

 - FastCGI

    - FastCGI（Fast Common Gateway Interface）全称是“快速通用网关接口”，也是一种让 客户端 与 Web服务器 程序进行通信（数据传输）的协议。。
    - `FastCGI` 是 `CGI` 模式的升级版, 目的是避免重复解析配置文件和初始执行环境。
    - 像是一个常驻型 `CGI` , 可以一直处理请求不结束该进程。
    - 多进程，将比 `CGI` 消耗更多的服务器内存。
    - 可平滑停止/启动进程。

 - PHPCGI

    - 一个 `CGI` 程序，是 PHP 实现 `CGI` 的 PHP解析器。
    - 用于解析请求，返回结果。
    - 不可平滑重启。

 - PHP-FPM

    - `PHP-FPM` 为 `FastCGI` 的进程管理器。
    - 工作原理为:
    - Web 服务器启动时，加载启动 `PHP-FPM`，`PHP-FPM` 读取配置文件，初始化运行环境。
    - `PHP-FPM` 创建一个 Master 主进程和若干个 Worker 进程，负责监听端口，等待接收请求，每个进程内都调用一个 `PHP-CGI`。
    - 用户发起请求, Web服务器接收请求并转发给 `PHP-FPM`，空闲的 Worker 进程以抢占式的接收该请求。
    - 监听接收后，`PHPCGI` 解析请求，开始执行业务处理代码, 处理完成后，按照 CGI 规定的格式返给 Worker 进程, 然后退出进程, 此时 Worker 进程变成空闲状态等待下次请求。
    - Worker 进程将结果返给 Web服务器, Web服务器接收返回内容并返回给客户端。

 - MODULE

    - `apache + php` 运行时，默认使用的是 `module 模式`，它把 php 作为 `apache` 的模块随 `apache` 启动而启动，接收到用户请求时则直接通过调用 `mod_php 模块` 进行处理。


 - PHP-CLI

    - `PHP-CLI 模式` 属于命令行模式
    - 在终端直接输入 `php 文件名.php` 就可直接运行代码
    - 没有超时时间
    - `echo`、`var_dump`、`phpinfo` 等输出会直接打印到控制台中

### PHP 数组底层原理

1. 底层实现是通过散列表（hash table） + 双向链表（解决hash冲突）
    1. hashtable：将不同的关键字（key）通过映射函数计算得到散列值（Bucket->h） 从而直接索引到对应的Bucket
    2. hash表保存当前循环的指针, 所以foreach 比for更快
    3. Bucket：保存数组元素的key和value, 以及散列值h

2. 如何保证有序性
    1. 散列函数和元素数组（Bucket）中间添加一层大小和存储元素数组相同的映射表。
    2. 用于存储元素在实际存储数组中的下标
    3. 元素按照映射表的先后顺序插入实际存储数组中
    4. 映射表只是原理上的思路, 实际上并不会有实际的映射表, 而是初始化的时候分配Bucket内存的同时, 还会分配相同数量的 uint32_t 大小的空间, 然后将 arData 偏移到存储元素数组的位置。

3. 解决hash重复(php使用的链表法)：
    1. 链表法:不同关键字指向同一个单元时, 使用链表保存关键字（遍历链表匹配key）
    2. 开放寻址法：当关键字指向已经存在数据的单元的时候, 继续寻找其他单元, 直到找到可用单元（占用其他单元位置, 更容易出现hash冲突, 性能下降）

4. 基础知识
    1. 链表：队列、栈、双向链表
    2. 链表：元素 + 指向下一元素的指针
    3. 双向链表：指向上一元素的指针 + 元素 + 指向下一元素的指针

### 依赖注入实现方式

 1. 构造函数依赖注入（如果依赖的类多，就会造成构造函数的形参特别多）
 2. set 方式注入（如果依赖的类多，那 set 的方法也特别多）
 3. 采用类似 Laravel 服务容器 实现依赖注入（调用时使用闭包，这样就做到 使用才实例化）

### PHP 内存溢出解决

 1. 增加 PHP 可用内存大小
 2. 对大数组分批处理或 yield 处理
 3. 及时销毁大数组或变量
 4. 根据业务规则，尽可能的少用 静态变量
 5. 数据库操作完，及时关闭

## 对比篇

### isset和empty的区别？

`Isset`测试变量是否`被赋值`，如果这个变量没被赋值，则返回false，`empty`是判断变量是否`为空`，当赋值为0，null,’’,返回true为真。他们之间最大的区别就是当一个变量被赋值0时，empty判断它为空，而isset判断它有值不为空。

### define() 与 const 区别
 
 - 两者都是定义常量使用
 - const 是语言结构, define 是函数
 - const 可在类中使用, define 不可以
 - const 可以不同命名空间定义相同名称的常量, define 不可以
 - const 大小写敏感, define 默认敏感, 可通过第三个参数为 true 设置为不敏感

### include 和 require 的区别是什么？
 - require 是无条件包含, 也就是如果一个流程里加入 require , 无论条件成立与否都会先执行 require , 当文件不存在或者无法打开的时候, 会提示错误, 并且会终止程序执行
 - include有返回值, 而require没有 (可能因为如此 require 的速度比 include 快), 如果被包含的文件不存在的话, 那么会提示一个错误, 但是程序会继续执行下去

### 单引号与双引号的区别

 - 单引号不解析变量，双引号解析变量
 - 单引号只可解析单引号及转义符本身，双引号可解析更多的特殊字符。例: `\n`、`\r`、`\t`
 - 解析速度不同，因单引号不考虑变量解析，所以比双引号要快

### 传值与传引用的区别

 - 按值传递 ：函数范围内对值的任何改变在函数外部都会被忽略
 - 按引用传递 ：函数范围内对值的任何改变在函数外部也能反映出这些修改, 因为传引用传的是内存地址。
 - 优缺点：按值传递时, php 必须复制值。特别是对于大型的字符串和对象来说, 这将会是一个代价很大的操作。按引用传递则不需要复制值, 对于性能提高很有好处。

### == 与 === 的区别
    == 要求两侧的值相同，弱类型判断
    
    === 要求两侧的值与类型都得相同

### echo、print、print_r、var_dump 的区别
 - print_r 与 var_dump 是函数, echo、print 是语句
 - `echo` 用于输出数值变量或字符串，可以逗号分隔输出多个。数组输出 Array, 对象报错。例: `echo $a, $b;`
 - `print` 用于输出数值变量或字符串, 不可输出多个。数组输出 Array, 对象报错。例: `print $a;`
 - `print_r` 可简单输出 字符串、数字、数组、对象, 但 布尔(false)、null 都是打印 `\n`
 - `var_dump` 可输出所有字符串、数字、布尔、数组、对象。包括键、值、类型、长度。

### for 与 foreach 的区别，哪个更快？为什么？
 - for 需要预先知道数组的长度, foreach 不需要
 - foreach 效果要比 for 高，foreach 直接通过结构体中的 next 指针获取下一个值, 而 for 循环需要根据 key 先进行一次 hash 才得到值。

## 面向对象篇

### 一、 面向对象基本知识
#### 1.1 什么是类
 - 类是事物相关属性特征和行为特征的集合。

   - 属性特征: 就是该事物的状态。比如： 用户性别、用户的身高。

   - 行为特征: 就是该事物能够做什么。比如： 用户下单、用户评论。

 - ⚠️ 注意

   - 属性特征在类中被称为： 成员属性或成员变量

   - 行为特征在类中被称为： 成员方法

#### 1.2 什么是对象
 - 对象是客户存在的一个实例，是事物的具体实现。可以通过对象调用类的属性 与 行为。

#### 1.3 类与对象的关系
 - 类是对对象抽象的一个描述
 - 对象是客观存在的一个实体

#### 1.4 PHP创建类的示例
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

### 二、 类、属性、方法的修饰符
#### 2.1 类的修饰符
 - 类的修饰符有: `abstract`、`final`、`interface`、`trait`

#### 2.2 成员方法的修饰符
 - 成员方法的修饰符有: `public`、`protected`、`private`、`static`、`abstract`、`final`

#### 2.3 成员属性修饰符
 - 属性修饰符有: public、protected、private、static、var
 - var 与 public 作用相同, var 是 public 的别名。

#### 2.4 static 静态修饰符
示例请看上边`1.5`的代码

 - `static` 静态修饰符 : 用于修饰类的成员属性和成员方法 。

 - `static` 关键字 : 修饰的成员方法称为静态方法，修饰的成员属性称为静态属性。


调用方法:

  1. 可以不用 new（实例化）就可以直接调用, 格式 类名::属性名
  2. 静态方法在实例化后的对象也可以访问, 格式 对象名->属性名

⚠️注意:

  1. 在静态方法中不可以使用非静态的内容。就是不让使用 $this
  2. 在类的方法中可以使用其他静态属性和静态方法，不过要使用self关键字，如 self::静态属性名 或 self::静态方法名

#### 2.5 final 修饰符
 - 类使用时: 如果类使用 final关键字修饰时 ，表示这个类不可以有子类，即不能被 继承。

 - 成员方法使用时: 如果成员方法 使用 final 关键字修饰时，表示这个成员方法不可以在子类中被覆盖，即不能被 重写。

#### 2.6 abstract 抽象修饰符
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

#### 2.7 interface 接口修饰符
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

### 三、 面向对象三大特性
 - 三大特性: 封装、继承、多态。

#### 3.1 封装
 - 我理解的 封装 就是 类的定义, 将事物相关属性特征和行为特征的集合在一起，形成一个 类，这就是封装。

#### 3.2 继承
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

#### 3.3 多态
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

### 四、 面向对象的七大原则
    单一职责原则、开放封闭原则、里式替换原则、依赖倒置原则、接口隔离原则、迪米特原则、合成/聚合复用原则

#### 4.1 单一职责原则
 - 单一职责原则(Single Responsibility Principle，SRP): 一个类应仅有一个职责。

 - 通俗的讲就是一个类当中的所有成员方法完成的工作相关性是相同的。

 - 例: 一个类不能即完成用户的注册, 又完成商品的购买。

#### 4.2 开放封闭原则
 - 开放封装原则(Open-Closed Principle，OCP): 即对扩展开放对修改关闭。

 - 通俗的讲就是 在不修改源代码的情况下，对其扩展，不影响原有功能。

#### 4.3 里式替换原则
 - 里式替换原则(Liskov Substitution Principle ，LSP): 所有引用基类的地方必须能透明地使用其子类的对象。

 - 通俗的讲就是 子类可扩展父类, 而不是覆盖父类或改变父类原有的功能。

#### 4.4 依赖倒置原则
 - 依赖倒置原则(Dependency Inversion Principle ，DIP): 依赖于抽象。

 - 通俗的讲:

    1. 高层次模块不应该依赖于低层次模块，两者都应依赖于抽象
    2. 抽象不应该依赖细节，细节应该依赖于抽象。

#### 4.5 接口隔离原则
 - 接口隔离原则(Interface Segregation Principle, ISP): 使用多个小且专门的接口, 不要使用一个大的总接口。

 - 通俗的讲就是: 不要把所有功能都写在一个接口中，干啥的就是干啥的，且不应该依赖那些不需要用不着的接口。

#### 4.6 迪米特原则
 - 迪米特原则(Law of Demeter ，LoD): 降低类与类之间的耦合。

 - 通俗的讲就是: 没有关系的类别硬往一起扯，减少没用的交际。

#### 4.7 合成/聚合复用原则
 - 合成/聚合复用原则(Composite/Aggregate Reuse Principle ，CARP): 尽量使用对象组合，而不是继承来达到复用的目的。

 - 通俗的讲就是: PHP类之间的关系，尽可能的多使用 trait和 use ，少使用 extends。

## 算法排序篇

### 快速排序
```php
function quick_sort($array) {

   if (count($array) <= 1) return $array;

   $array_count = count($array); # 数组数量
   $key = $array[0]; # 对比值
   $left_arr = array(); # 接收小于对比值的数
   $right_arr = array(); # 接收大于对比值的数

   for ($i=1; $i<$array_count; $i++){
       if ($array[$i] <= $key){
           $left_arr[] = $array[$i];
       }else{
           $right_arr[] = $array[$i];
       }
   }
   $left_arr = quick_sort($left_arr);
   $right_arr = quick_sort($right_arr);
   return array_merge($left_arr, array($key), $right_arr);
}
```
### 冒泡排序
```php
$list = [2, 4, 1, 7, 9, 3];
$len = count($list);

for ($i = $len - 1; $i > 0; $i--) {
   $flag = 1;
   for ($j = 0; $j < $i; $j++) {
       if ($list[$j] > $list[$j + 1]) {
           $tmp = $list[$j];
           $list[$j] = $list[$j + 1];
           $list[$j + 1] = $tmp;
           $flag = 0;
       }
   }
   if($flag) break;
}
var_dump($list);
```

### 二分查找
```php
//二分查找（数组里查找某个元素）
function bin_sch($array, $low, $high, $k){
   if ($low <= $high){
       $mid = intval(($low+$high)/2);
       if ($array[$mid] == $k){
           return $mid;
       }elseif ($k < $array[$mid]){
           return bin_sch($array, $low, $mid-1, $k);
       }else{
           return bin_sch($array, $mid+1, $high, $k);
       }
   }
   return -1;
}
```

### 顺序查找
```php
function seq_sch($array, $n, $k){
    $array[$n] = $k;
    for($i=0; $i<$n; $i++){
        if($array[$i]==$k){
            break;
        }
    }
    if ($i<$n){
        return $i;
    }else{
        return -1;
    }
}
```

### 插入排序
```php
function insertSort($arr)
{
   $count = count($arr);
   for ($i = 1; $i < $count; $i++) {
       $tmp = $arr[$i];
       for ($j = $i - 1; $j >= 0; $j--) {
           // 从小到大 【<】 从大到小 【>】
           if ($tmp < $arr[$j]) {
               $arr[$j] = $arr[$j + 1];
               $arr[$j + 1] = $tmp;
           } else {
               break;
           }
       }
   }
   return $arr;
}
```

### 选择排序
```php
function selectSort($arr){
   for ($i=1;$i<count($arr);$i++){
       $p = $i;
       for ($j = $i + 1; $j < count($arr);$j++){
           if ($arr[$p] > $arr[$j]){
               $p = $j;
           }
       }
       if ($p != $i){
           $tmp = $arr[$p];
           $arr[$i] = $tmp;
           $arr[$p] = $arr[$i];
       }
   }
   return $arr;
}
```

### 写一个二维数组排序算法函数
```php
/**
* 二维数组排序
* @param $arrays
* @param $sort_key
* @param $sort_order (SORT_DESC 降序；SORT_ASC 升序)
* @param $sort_type (请看官方文档 array_multisort 函数的说明)
* @return array|false
*/
function array_sort($arrays,$sort_key,$sort_order=SORT_DESC,$sort_type=SORT_NUMERIC ){
   if(is_array($arrays)){
       foreach ($arrays as $array){
           if(is_array($array)){
               $key_arrays[] = $array[$sort_key];
           }else{
               return false;
           }
       }
   }else{
       return false;
   }
   array_multisort($key_arrays,$sort_order,$sort_type,$arrays);
   return $arrays;
}
```

## 实践篇

### 微信实际支付成功, 但回调失败如何处理？
 - 临时页面处理：在返回页增加 “支付成功” 与 “遇到问题, 联系客服” 按钮选项。这两个按钮都重新调取微信获取支付结果的接口，成功或失败都跳转一个中间页。

 - 定时处理：如没有临时页, 则根据业务情况, 设置合适的回调周期, 周期性的调取 “获取微信支付结果的接口” , 将支付结果更新至数据库。

### 如何获取客户端 IP 与服务端 IP
```php
# 客户端IP
    $_SERVER['REMOTE_ADDR']
# 服务端IP
   $_SERVER['SERVER_ADDR']
# 客户端IP(代理穿透)
   $_SERVER['HTTP_X_FORWARDED_FOR']

/*
* 获取客户端IP地址
* @return string
*/
function get_client_ip() {
   if(getenv('HTTP_CLIENT_IP')){
       $client_ip = getenv('HTTP_CLIENT_IP');
   } elseif(getenv('HTTP_X_FORWARDED_FOR')) {
       $client_ip = getenv('HTTP_X_FORWARDED_FOR');
   } elseif(getenv('REMOTE_ADDR')) {
       $client_ip = getenv('REMOTE_ADDR');
   } else {
       $client_ip = $_SERVER['REMOTE_ADDR'];
   }
   return $client_ip;
}


/* 获取服务器端IP地址
* @return string
*/
function get_server_ip() {
   if (isset($_SERVER)) {
       if($_SERVER['SERVER_ADDR']) {
           $server_ip = $_SERVER['SERVER_ADDR'];
       } else {
           $server_ip = $_SERVER['LOCAL_ADDR'];
       }
   } else {
       $server_ip = getenv('SERVER_ADDR');
   }
   return $server_ip;
}
```
### 不使用临时变量交换两个变量的值
```php
list($a, $b) = array($b, $a);

# 或 数组下标
$array[0] = $a;
$array[1] = $b;
$a = $array[1];
$b = $array[0];
```

### 通过 $_FILES 获取上传文件类型可能受到黑客伪造, 如何判断用户上传的图像文件类型真实可靠
 - getimagesize 函数获取的数组结果下标为 2 的值代表文件的类型。
```json
1 = GIF, 2 = JPG, 3 = PNG, 4 = SWF, 5 = PSD, 6 = BMP, 7 = TIFF(intel byte order), 8 = TIFF(motorola byte order), 9 = JPC, 10 = JP2, 11 = JPX, 12 = JB2, 13 = SWC, 14 = IFF, 15 = WBMP, 16 = XBM,
```

### 短信验证码防刷机制
 - 前端时间控制：60 秒后才能再次发送，但刷新页面就会又能发送
 - Token 校验：校验通过才发送，这时还可以将 60 秒缓存
 - 图形验证码限制
 - 次数限制：根据业务场景，例： 同一手机号，24小时内不可超过5条
 - 相同返回：例： 30 分钟之内，如果验证码未使用，则返回同一个验证码
 - 短信预警机制：例：检测短时发送量，达到预警值，就给管理员发送提醒。
 - IP限制

### 如何实现 session 共享
 - 将 session 持久化至数据库
 - 将 session 保存 至 Redis、Memcache

### PHP 如何解决跨域问题？
```php
# 1. 代理, 由 php 调用 php 接口

# 2. Nginx 反向代理

# 3. 允许所有域名访问
header(“Access-Control-Allow-Origin:*”);
header(‘Access-Control-Allow-Methods:POST’);// 表示只允许POST请求
header(‘Access-Control-Allow-Headers:x-requested-with, content-type’);

# 4. 允许单个域名访问
header(‘Access-Control-Allow-Origin:http://www.test.cn‘);
header(‘Access-Control-Allow-Methods:POST’); //表示只允许POST请求
header(‘Access-Control-Allow-Headers:x-requested-with, content-type’); //请求头的限制

# 5. 允许多个域名访问
$array = ['域名1', '域名2'];
public function setheader()
{
    // 获取当前跨域域名
    $origin = isset($_SERVER[‘HTTP_ORIGIN’]) ? $_SERVER[‘HTTP_ORIGIN’] : ‘’;
    if (in_array($origin, $array)) {
        header(‘Access-Control-Allow-Origin:’ . $origin); # 允许 $array 数组内的域名跨域访问
        header(‘Access-Control-Allow-Methods:POST,GET’); # 响应类型
        header(‘Access-Control-Allow-Credentials: true’); # 带 cookie 的跨域访问
        header(‘Access-Control-Allow-Headers:x-requested-with,Content-Type,X-CSRF-Token’); # 响应头设置
    }
}
```

### 字符串反转
```php
<?php
    function str_rev ($str) {
        # true 模拟死循环, $i 为长度
        for ($i = 0; true; $i++) //true模拟死循环
        {
            if (!isset($str[$i])) break;
        }
        $return_str = '';
        for ($j = $i - 1; $j >=0 ; $j -- )
        {
            $return_str .= $str[$j];
        }
        return $return_str;
    }

    # 或

    function str_rev($str,$encoding='utf-8'){
        $result = '';
        $len = mb_strlen($str);
        for($i=$len-1; $i>=0; $i--){
            $result .= mb_substr($str,$i,1,$encoding);
        }
        return $result;
    }

```

### 自己写一个获取字符串长度的函数
```php
function strlen($str)   { 
    if ($str == '') return 0; 
    $count = 0; 
    while (1){ 
        if ($str[$count] != NULL){ 
            $count++; 
            continue; 
        }else{ 
            break; 
        } 
    } 
    return $count; 
}

```

### 写一个可以从 URL 链接中取出文件扩展名的函数
```php
function getExt($url)
{
    $arr = parse_url($url);//parse_url解析一个 URL 并返回一个关联数组，包含在 URL 中出现的各种组成部分
    //'scheme' => string 'http' (length=4)
    //'host' => string 'www.sina.com.cn' (length=15)
    //'path' => string '/abc/de/fg.php' (length=14)
    //'query' => string 'id=1' (length=4)
    $file = basename($arr['path']);// basename函数返回路径中的文件名部分
    $ext = explode('.', $file);
    return $ext[count($ext)-1];
}
```

### PHP遍历文件夹
 - 遍历某一个目录下面的文件和文件夹
```php
$dir = __DIR__;
if (is_dir($dir)) {
   $array = scandir($dir, 1);
   foreach($array as $key => $value) {
       if ($value == '.' || $value == '..') {
           unset($array[$key]);
           continue;
       }
   }
} else {
   echo '不是一个目录';
}

print_r($array);
```

 - 写出一个函数对文件目录坐遍历
```php
function loopDir($dir){
   $handle = opendir($dir);
   while(false !==($file =readdir($handle))){
       if($file!='.'&&$file!='..'){
           echo $file."<br>";
           if(filetype($dir.'/'.$file)=='dir'){
               loopDir($dir.'/'.$file);
           }
       }
   }
}
$dir = '/';
loopDir($dir);
```

 - 遍历某个目录下面的所有文件和文件夹(包含子文件夹的目录和文件也要依次读取出来)
```php
$dir = __DIR__;
function my_dir($dir) {
   $files = array();
   if(@$handle = opendir($dir)) {
       while(($file = readdir($handle)) !== false) {
           if($file != ".." && $file != ".") {
               if(is_dir($dir."/".$file)) { 
                   $files[$file] = my_dir($dir."/".$file);
               } else { 
                   $files[] = $file;
               }
           }
       }
       closedir($handle);
       return $files;
   }
}
print_r(my_dir($dir));
```

### 写一个函数, 将 “open_door” 转为 “OpenDoor”
```php
function ucstring($string){
    return str_replace(' ', '', ucwords(str_replace('_', ' ', $string)));
}

# 或

function ucstring($string){
    $array = explode('_', $string);
    foreach($array as $key=>$val){
        $new_string .= ucwords($val);
    }
    return $new_string;
}
```
### 写一个函数, 将 1234567890 转为 1,234,567,890 逗号隔开
```php
function numFormate($number){
    $str = (string) $number;
    $string = strrev($str); # 先反转
    $length = strlen($string); # 获取长度
    for($i = 0; $i < $length; $i = $i+3)
    {
        $new_string .= substr($string, $i, 3) . ',';
    }
    return strrev(rtrim($new_string, ','));
}
```

### 获取扩展名
```php
function get_ext1($file_name){
   return strrchr($file_name, ‘.’);
}

function get_ext2($file_name){
   return substr($file_name,strrpos($file_name, ‘.’));
}

function get_ext3($file_name){
   return array_pop(explode(‘.’, $file_name));
}

function get_ext4($file_name){
   $p = pathinfo($file_name);
   return $p['extension'];
}

function get_ext5($file_name){
   return strrev(substr(strrev($file_name), 0, strpos(strrev($file_name), ‘.’)));
}

function getExt($url){

   $arr = parse_url($url);

   $file = basename($arr['path']);

   $ext = explode(“.”,$file);

   return $ext[1];
}
```

### 求两个日期的差数, 例如2022-2-5 ~ 2022-3-6 的日期差数
```php
function get_days($date1, $date2){

   $time1 = strtotime($date1);

   $time2 = strtotime($date2);

   return ($time2-$time1)/86400;
}
```

### 写出一个函数，参数为年份和月份，输出结果为指定月的天数
```php
function getDayCount($year, $month) {
    $date_string = $year . '-' . $month . '-1';
    return date('t', strtotime($date_string));
}
```

### 获取今天是本月所在的第几周
```php
echo ceil(date('d')/7);
```