?> 复习整理PHP相关的学习资料


> PHP

### isset和empty的区别？

`Isset`测试变量是否`被赋值`，如果这个变量没被赋值，则返回false，`empty`是判断变量是否`为空`，当赋值为0，null,’’,返回true为真。他们之间最大的区别就是当一个变量被赋值0时，empty判断它为空，而isset判断它有值不为空。

### 面向对象的七大原则
    单一职责原则
    开放封闭原则
    里式替换原则
    依赖倒置原则
    接口隔离原则
    迪米特原则
    合成/聚合复用原则

### php基本变量类型
    四种标量类型 ：boolean （布尔型）、integer （整型）、float （浮点型, 也称作 double)、string （字符串）

    四种复合类型 ：array （数组）、object （对象）、callable、iterable

    最后是两种特殊类型 ：resource（资源）、NULL（NULL）

### PHP魔术方法
    13个常用的魔术方法
        __construct、 __destruct、 __call 或 __classStatic、 __get、 __set、 __isset、 __unset、 __toString、 __clone、 __sutoload、 __invoke、 __sleep、 __wakeup

    1. __construct 构造方法
        __construct构造方法，当一个类被实例化创建对象时，会首先执行构造方法。
    
    2. __destruct 析构方法
        __destruct 析构方法， 当对象在销毁之前或失去对对象的引用时，会调用 析构方法。
    
    3. __call 或 __callStatic
        当调用一个未定义的或没有权限的成员方法时，会调用 __call 方法。（当在静态方法中调用一个未定义的或没有权限的成员方法时，则会调用 __callStatic 方法。）
        如果本类找不到调用的成员方法，会去父类中找。
        如果本类找不到 __call 方法，会去父类中找。
    
    4. __get
        当调用一个未定义的或非公有的成员属性时，会调用 __get 方法。

    5. __set
        当给一个未定义的或非公有的成员属性赋值时， 会调用 __set 方法。

    6. __isset
        当在一个未定义的或非公有的成员属性上调用 isset函数时，会调用 __isset 方法。

    7. __unset
        当在一个未定义或非公有的成员属性上调用 unset函数时，会调用 __unset 方法。

    8. __toString
       在打印输出一个对象时, 会自动调用 __toString 方法。 例: echo 对象名。
    
    9. __clone
        当克隆一个对象时, 会自动调用 __clone 方法。 例: $clone_obj = clone 对象名;
    
    10. __autoload
        在实例化一个尚未被定义的类时会自动调用 __autoload 来加载类文件。

    11. __invoke
        当尝试以调用函数的方式调用一个对象时, 会自动调用 __invoke 方法。

    12. __sleep
        serialize() 函数会检查类中是否存在 __sleep 方法，如果存在，先执行 __sleep 方法，再执行 序列化操作。
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
    
    13. __wakeup
        unserialize() 函数会检查类中是否存在 __wakeup 方法，如果存在，先执行 __wakeup 方法，再执行 反序列化操作。
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

    名称               说明
    __LINE__           文件中的当前行号
    __FILE__           文件的完整路径和文件名。如果用在被包含文件中，则返回被包含的文件名。
    __DIR__            文件所在的目录。如果用在被包括文件中，则返回被包括的文件所在的目录。它等价于 dirname(__FILE__)。除非是根目录，否则目录中名不包括末尾的斜杠。
    __NAMESPACE__      当前命名空间的名称。
    __TRAIT__          Trait 的名字。Trait 名包括其被声明的作用域（例如 Foo\Bar）。
    __CLASS__          当前类的名称。类名包括其被声明的作用域（例如 Foo\Bar）。当用在 trait 方法中时，CLASS 是调用 trait 方法的类的名字。
    __FUNCTION__       当前函数的名称。匿名函数则为 {closure}。
    __METHOD__         类的方法名。
    ClassName::class   完整的类名。

### PHP超全局变量
    PHP 有 9 个超全局变量:
        $_SERVER、$_GET、$_POST、$_REQUEST、$_COOKIE、$_SESSION、$_FILES、$_ENV、$GLOBALS
    
    1. $_SERVER
        $_SERVER: 一个包含了诸如头信息（header）、路径（path）、以及脚本位置（srcipt location）等信息的数组。这个数组中的项目由Web服务器创建。

        常用 $_SERVER 中的参数

        参数	                        描述
        $_SERVER['SERVER_NAME']         当前运行脚本所在服务器主机的名称
        $_SERVER['REQUEST_METHOD']      访问页面时的请求方法。例如：GET、HEAD，POST，PUT
        $_SERVER['QUERY_STRING']	    查询(query)的字符串。例如: www.bqhub.com?a=1 。 则 获取到的值为 “a=1”
        $_SERVER['REQUEST_URI']         访问此页面所需的URI。例如: www.bqhub.com?a=1 。 则 获取到的值为 “/?a=1”
        $_SERVER['SCRIPT_NAME']         包含当前脚本的路径。 例如: index.php
        $_SERVER['PHP_SELF']            当前正在执行的脚本文件名。
        $_SERVER['REMOTE_ADDR']         当前页面用户的IP地址。
        $_SERVER['REMOTE_HOST']         当前页面用户的主机名。

    2. $_GET
        $_GET 可以获取到使用 get 方法传递的参数的相关信息。

    3. $_POST
        $_POST 可以获取到使用 post 方法传递的参数的相关信息。

    4. $_REQUEST
        $_REQUEST 是一个关联数组，默认包含 $_GET、$_POST、$_COOKIE 中的内容。建议不用这个超级变量，因为它不够安全。

    5. $_COOKIE
        $_COOKIE 是一个关联数组，包含 通过 HTTP cookie 传递给当前脚本的内容。

    6. $_SESSION
        $_SESSION 是一个关联数组，包含当前脚本中的所有 session 内容。

    7. $_FILES
        $_FILES 是一个关联数组，包含通过 HTTP POST 方法上传给当前脚本的文件内容。

    8. $_ENV
        $_ENV 是一个包含服务器端环境变量的数组。

    9. $GLOBALS
        $GLOBALS 是一个关联数组， 包含对当前脚本全局 范围内定义的所有变量。

