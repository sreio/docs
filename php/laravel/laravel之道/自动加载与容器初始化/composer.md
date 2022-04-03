## 简介

> 本篇主要剖析 Composer 如何做类的自动加载

剖析过程主要以 `http://localhost/register` 请求的生命周期为主线，暂不涉及 if 语句不会执行到的代码。

## 源代码阅读方式指南

本教程源代码剖析，能在代码注释中写明的，不会放到外面进行剖析，一些补充可能会放在代码后面进行单独说明。

因为代码执行涉及多个文件，且每个文件之间存在父与子的树形结构，故代码阅读方式，请遵从树的 **前序遍历** 原则，防止阅读迷茫。

代码执行顺序由 (1)、(2)、(3) ... (n) 方式标注

## 注释中特殊字符说明

`[请到 <<某某小标题代表的文件>> 继续阅读]` : 代表此行代码调用了另一个文件的方法，你应该去那个文件阅读，否则不明白是如何执行的。

## Laravel 入口文件

`public/index.php`
```php
<?php

// (1) 定义了一个 LARAVEL_START 的常量，并赋值了小数点化的微秒数，作用就是记录 Laravel 启动的时间点
define('LARAVEL_START', microtime(true));

// (2) 将导入 vendor/autoload.php 文件，并执行此文件，将此文件中的变量、类、函数注入 PHP 运行内存中 [请到 <<Composer 自动加载的入口文件>> 继续阅读]
require __DIR__.'/../vendor/autoload.php';
// (3) Composer 自动加载原理完结

$app = require_once __DIR__.'/../bootstrap/app.php';

$kernel = $app->make(Illuminate\Contracts\Http\Kernel::class);

$response = $kernel->handle(
    $request = Illuminate\Http\Request::capture()
);

$response->send();

$kernel->terminate($request, $response);
```

- 额外注意

  在 PhpStrom 步调工具中，如果点击步入函数按钮，将进入 `vendor/autoload.php` 中进行步调；如果点击跳过函数按钮，将在当前文件跳到下一行，并把 `vendor/autoload.php` 中的变量、类、函数直接注入内存中，无论 `vendor/autoload.php` 内部调用多少类、多少函数，直接全部执行完毕。

-  知识扩展

  `__DIR__`：返回当前文件所在绝对路径，记住是当前文件哦；不是 `index.php`的绝对路径，是有 `__DIR__` 代码的文件所在绝对路径哦。
  
## Composer 自动加载的入口文件

> 请看完 `<<Laravel 入口文件>>` 第 (2) 步，再看此文件

`vendor/autoload.php`
```php
<?php

// (1) 真 • Composer 入口文件
require_once __DIR__ . '/composer/autoload_real.php';

// (2) 返回自动加载对象，我们可以用它做动态类名绑定哦 [请到 <<真 • Composer 自动加载入口文件>> 继续阅读]
return ComposerAutoloaderInite12a16f2d7bcbc7a72b9f10faab9dc8b::getLoader();
```

## 真 • Composer 自动加载入口文件

> 请看完 `<<Composer 自动加载的入口文件>>` 的第 (2) 步，再看此文件

`vendor/composer/autoload_real.php`
```php
<?php

class ComposerAutoloaderInite12a16f2d7bcbc7a72b9f10faab9dc8b
{
    /**
     * 静态对象类型变量，类似单例模式的只有一个对象，防止二次实例化
     */
    private static $loader;

    /**
     * 先有鸡后有蛋，想自动加载其它类，先自动加载自己吧
     */
    public static function loadClassLoader($class)
    {
        // (4) $class 形参就是代表 PHP 没有载入的类全名（包含命名空间，且开头的 \ 去掉）
        if ('Composer\Autoload\ClassLoader' === $class) {
            // (5) 既然没有载入，那么就载入吧
            require __DIR__ . '/ClassLoader.php';
        }
    }

    /**
     * 自动加载主方法，等于 C 中 main 函数，反正是主要的就是啦
     */
    public static function getLoader()
    {
        // (1) 类似单例模式的操作
        if (null !== self::$loader) {
            return self::$loader;
        }

        // (2) 将 loadClassLoader 静态方法添加至 PHP 自动加载队列
        // spl_autoload_register 注册给定的函数作为 __autoload 的实现
        spl_autoload_register(array('ComposerAutoloaderInite12a16f2d7bcbc7a72b9f10faab9dc8b', 'loadClassLoader'), true, true);

        // (3) PHP没有载入 Composer\Autoload\ClassLoader 类，去找 PHP 自动加载队列，并执行里面的 loadClassLoader，
        // 然后载入 Composer\Autoload\ClassLoader 类，成功 new 一个对象。
        self::$loader = $loader = new \Composer\Autoload\ClassLoader();

        // (6) 将 loadClassLoader 静态方法从 PHP 自动加载队列中删除
        spl_autoload_unregister(array('ComposerAutoloaderInite12a16f2d7bcbc7a72b9f10faab9dc8b', 'loadClassLoader'));

        /*
         * (7)
         *
         * 第一个表达式 PHP_VERSION_ID >= 50600: 由于我用的是 PHP7.2 故，此表达式是 true，与运算未短路，执行下一表达式
         * 第二个表达式 !defined('HHVM_VERSION'): 没有用 HHVM 虚拟机则返回 true，我的确没用，未短路，继续下一个表达式
         * 第三个表达式 (!function_exists('zend_loader_file_encoded') || !zend_loader_file_encoded()) 分解如下: 
         * 第三个表达式中第一个表达式 !function_exists('zend_loader_file_encoded'): 如果不存在 zend_loader_file_encoded 函数返回 true。经过测试我 PHP 中的确没有 zend_loader_file_encoded 函数，故与第三表达式中第二表达式短路。
         * 整体返回 true
         */
        $useStaticLoader = PHP_VERSION_ID >= 50600 && !defined('HHVM_VERSION') && (!function_exists('zend_loader_file_encoded') || !zend_loader_file_encoded());


        if ($useStaticLoader) {

            // (8) 加载 vendor\composer\autoload_static.php 静态的自动加载映射类
            require_once __DIR__ . '/autoload_static.php';

            // (9) 调用静态自动加载映射类的 getInitializer 方法，此方法将返回一个闭包，再通过执行此闭包，将自动加载映射规则注入到 loader 对象中 [请到 <<Composer 自动加载映射类>> 继续阅读]
            call_user_func(\Composer\Autoload\ComposerStaticInite12a16f2d7bcbc7a72b9f10faab9dc8b::getInitializer($loader));
        } else {
            $map = require __DIR__ . '/autoload_namespaces.php';
            foreach ($map as $namespace => $path) {
                $loader->set($namespace, $path);
            }

            $map = require __DIR__ . '/autoload_psr4.php';
            foreach ($map as $namespace => $path) {
                $loader->setPsr4($namespace, $path);
            }

            $classMap = require __DIR__ . '/autoload_classmap.php';
            if ($classMap) {
                $loader->addClassMap($classMap);
            }
        }

        // (10) 调用 loader 对象的 register 方法，把 loadClass 方法添加到 PHP 自动加载队列中 [请到 <<Composer 核心自动加载类>> 继续阅读]
        $loader->register(true);

        if ($useStaticLoader) {

            // (11) 返回 Laravel 全局函数文件的绝对路径，准备加载全局函数
            $includeFiles = Composer\Autoload\ComposerStaticInite12a16f2d7bcbc7a72b9f10faab9dc8b::$files;
        } else {
            $includeFiles = require __DIR__ . '/autoload_files.php';
        }


        foreach ($includeFiles as $fileIdentifier => $file) {

            // (12) 取全局函数 文件标识码 和 文件绝对路径 执行循环加载
            composerRequiree12a16f2d7bcbc7a72b9f10faab9dc8b($fileIdentifier, $file);
        }

        return $loader;
    }
}

function composerRequiree12a16f2d7bcbc7a72b9f10faab9dc8b($fileIdentifier, $file)
{
    // (13) 根据全局函数 文件标识码 查看是否加载过，防止二次加载
    if (empty($GLOBALS['__composer_autoload_files'][$fileIdentifier])) {
        
        // (14) 执行全局函数加载
        require $file;

        // (15) 转换全局函数加载状态，防止二次加载
        $GLOBALS['__composer_autoload_files'][$fileIdentifier] = true;
    }
}
```

## Composer 自动加载映射类

> 请看完 `<<真 • Composer 自动加载入口文件>>` 的第 (9) 步，再看此文件

`vendor\composer\autoload_static.php`
```php
<?php

namespace Composer\Autoload;

class ComposerStaticInite12a16f2d7bcbc7a72b9f10faab9dc8b
{
    /**
     * 这些就是待加载的 Laravel 全局函数
     */
    public static $files = array (
        '0e6d7bf4a5811bfa5cf40c5ccd6fae6a' => __DIR__ . '/..' . '/symfony/polyfill-mbstring/bootstrap.php',

        // ... 还有更多，结构类似
    );

    /**
     * Psr4 键的长度；\\ 代表转义 \，实际长度为 1；算是一种索引优化，能够迅速定位 Psr4 加载规则所对应的键值对
     */
    public static $prefixLengthsPsr4 = array (
        'p' => 
        array (
            'phpDocumentor\\Reflection\\' => 25,
        ),
        'X' => 
        array (
            'XdgBaseDir\\' => 11,
        ),

        // ... 还有更多，结构类似
    );

    /**
     * Psr4 映射对应规则：数组键 是类所定义的命名空间的公共前缀，数组值 是所对应的实际文件的相对地址空间
     */
    public static $prefixDirsPsr4 = array (
        'phpDocumentor\\Reflection\\' => 
        array (
            0 => __DIR__ . '/..' . '/phpdocumentor/reflection-common/src',
            1 => __DIR__ . '/..' . '/phpdocumentor/reflection-docblock/src',
            2 => __DIR__ . '/..' . '/phpdocumentor/type-resolver/src',
        ),

        // ... 还有更多，结构类似
    );

    /**
     * Psr0 映射对应规则：与 Psr4 不同是数组结构不一样，但实现功能都是殊途同归的
     */
    public static $prefixesPsr0 = array (
        'P' => 
        array (
            'Prophecy\\' => 
            array (
                0 => __DIR__ . '/..' . '/phpspec/prophecy/src',
            ),
            'Parsedown' => 
            array (
                0 => __DIR__ . '/..' . '/erusev/parsedown',
            ),
        ),

        // ...还有更多，结构类似
    );

    /**
     * classMap 呵呵，类全名 对应 类所在绝对路径，简单的一比
     */
    public static $classMap = array (
        'App\\Console\\Kernel' => __DIR__ . '/../..' . '/app/Console/Kernel.php',
        'App\\Exceptions\\Handler' => __DIR__ . '/../..' . '/app/Exceptions/Handler.php',

        // ... 还有非常多，结构类似
    );


    public static function getInitializer(ClassLoader $loader)
    {
        // (1) 返回一个闭包，此闭包将在 <<真 • Composer 自动加载入口文件>> 第 (9) 步中通过 call_user_func 函数被调用。
        return \Closure::bind(function () use ($loader) {

            // (2) 下面这四行，代表将本文件中的 Psr4、Psr0 和 classMap 属性赋值到 loader 对象中。
            $loader->prefixLengthsPsr4 = ComposerStaticInite12a16f2d7bcbc7a72b9f10faab9dc8b::$prefixLengthsPsr4;
            $loader->prefixDirsPsr4 = ComposerStaticInite12a16f2d7bcbc7a72b9f10faab9dc8b::$prefixDirsPsr4;
            $loader->prefixesPsr0 = ComposerStaticInite12a16f2d7bcbc7a72b9f10faab9dc8b::$prefixesPsr0;
            $loader->classMap = ComposerStaticInite12a16f2d7bcbc7a72b9f10faab9dc8b::$classMap;

        }, null, ClassLoader::class);
    }
}
```

- 关于闭包函数（闭包对象）

  PHP 里面实现像 JavaScript 中的闭包，是通过一个内置对象实现的，这个对象是 [Closure](https://www.php.net/manual/zh/class.closure.php)。

  凡是对象都有属性和方法，同样 `Closure` 对象也有属性和方法。你肯定会问，既然是对象，怎么能通过 `Object()` 或者 `call_user_func('Object')` 来执行呢。那是因为 `Closure` 有一个魔术方法 ` __invoke()`。关于此方法看这 [__invoke()魔术方法](http://php.net/manual/zh/language.oop5.magic.php#language.oop5.magic.invoke)

- 关于闭包函数中的 use

  ```php
  function () use ($loader) {}
  ```

  因为闭包函数内与外是不同的空间，因此内部无法直接使用外部的变量的。只能通过 use 将外部的变量存放到 `Closure` 对象的 `static` 属性中，是以数组的形式存放的哦。这样在执行 `__invoke()` 时，就调用 `static` 中的键值对就可以了

- 注：$this 无需使用 use 引用可直接在 `Closure` 对象中使用。这个我理解的是，可能在类中声明的闭包对象默认继承自当前类，不知道对不

- 关于闭包函数的参数

  会存放在闭包对象的 `parameter` 属性中，也是以数组的形式存放

- 一个闭包对象的数据详情

  ![file](https://cdn.learnku.com/uploads/images/201809/27/27709/7fKu6JfSFD.png?imageView2/2/w/1240/h/0)

- 关于使用 `return \Closure::bind()` 方法，而不直接使用 `return function()` 一些想法
  
  你可能注意到，上面声明闭包的方式与传统的不一样。那是因为 ClassLoader 类里面 `$classMap` 等属性是私有属性，只用通过 `Closure::bind` 方法重新定义闭包所属类，才能在闭包中调用其它类的私有属性。

## Composer 核心自动加载类

> 请看完 `<<真 • Composer 自动加载入口文件>>` 的第 (10) 步，再看此文件

`vendor\composer\ClassLoader.php`
```php
<?php

namespace Composer\Autoload;

/**
 * 注：类里面的方法和属性顺序做了调整，并删除掉一些很少用的属性和方法，方便阅读
 */
class ClassLoader
{

    /**
     * 下面四个是前面被赋值的静态映射数组
     */
    private $prefixLengthsPsr4 = array();
    private $prefixDirsPsr4 = array();
    private $prefixesPsr0 = array();
    private $classMap = array();
    

    /**
     * 获取相应的数组映射
     */
    public function getClassMap()
    {
        return $this->classMap;
    }
    public function getPrefixesPsr4(){}
    public function getPrefixes(){}

    /**
     * 添加 ClassMap 额外的映射
     */
    public function addClassMap(array $classMap)
    {
        if ($this->classMap) {
            $this->classMap = array_merge($this->classMap, $classMap);
        } else {
            $this->classMap = $classMap;
        }
    }

    /**
     * 添加 Psr0 额外的映射
     */
    public function add($prefix, $paths, $prepend = false){}
    
    /**
     * 添加 Psr4 额外的映射
     */
    public function addPsr4($prefix, $paths, $prepend = false){}
    
    /**
     * 核心方法：将 Laravel 自动加载运行的方法 loadClass 添加至 PHP 自动加载序列
     */
    public function register($prepend = false)
    {
        // (1) 从 <<真 • Composer 自动加载入口文件>> 第 (10) 步过来的
        spl_autoload_register(array($this, 'loadClass'), true, $prepend);
    }

    /**
     * 将 Laravel 自动加载运行的方法从 PHP 自动加载序列中删除。
     */
    public function unregister()
    {
        spl_autoload_unregister(array($this, 'loadClass'));
    }

    /**
     * ！！！核心中的核心：以后的代码运行，碰到没有加载的类，就会来这里加载！！！
     */
    public function loadClass($class)
    {
        if ($file = $this->findFile($class)) {
            includeFile($file);

            return true;
        }
    }

    /**
     * 被核心中的核心调用了的方法，主要从 类 与 路径 映射中取出 类 所在的文件绝对路径，方便接下来的类加载
     */
    public function findFile($class)
    {
        // class map lookup
        if (isset($this->classMap[$class])) {
            return $this->classMap[$class];
        }

        // ...Psr0 和 Psr4 加载逻辑代码，这里暂且不看
    }
}

/**
 * 阻止包含文件访问 $this 或 self ：作用就是防止在待加载的 php 文件中使用 $this 或 self，从而直接访问了本 <<Composer 核心自动加载类>>，从而破坏因果关系，导致未知错误发生。说白了就是我的就是我的，不是我的，我也不能拿
 */
function includeFile($file)
{
    include $file;
}
```

## 全局函数文件简要说明

```php
public static $files = array (
        '0e6d7bf4a5811bfa5cf40c5ccd6fae6a' => __DIR__ . '/..' . '/symfony/polyfill-mbstring/bootstrap.php',
        '320cde22f66dd4f5d3fd621d3e88b98f' => __DIR__ . '/..' . '/symfony/polyfill-ctype/bootstrap.php',
        '25072dd6e2470089de65ae7bf11d3109' => __DIR__ . '/..' . '/symfony/polyfill-php72/bootstrap.php',
        '667aeda72477189d0494fecd327c3641' => __DIR__ . '/..' . '/symfony/var-dumper/Resources/functions/dump.php',
        '2c102faa651ef8ea5874edb585946bce' => __DIR__ . '/..' . '/swiftmailer/swiftmailer/lib/swift_required.php',
        'f0906e6318348a765ffb6eb24e0d0938' => __DIR__ . '/..' . '/laravel/framework/src/Illuminate/Foundation/helpers.php',
        '58571171fd5812e6e447dce228f52f4d' => __DIR__ . '/..' . '/laravel/framework/src/Illuminate/Support/helpers.php',
        '6124b4c8570aa390c21fafd04a26c69f' => __DIR__ . '/..' . '/myclabs/deep-copy/src/DeepCopy/deep_copy.php',
        '801c31d8ed748cfa537fa45402288c95' => __DIR__ . '/..' . '/psy/psysh/src/functions.php',
        '4e8671d7be9056dcd04ddd9e8e15f9cc' => __DIR__ . '/..' . '/encore/laravel-admin/src/helpers.php',
    );
```

`vendor/symfony/polyfill-mbstring/bootstrap.php`: Symfony 组件根据 PHP 的 mb_string 扩展定义了一些字符串处理的函数

`vendor/symfony/polyfill-php72/bootstrap.php`: 如果 PHP 版本小于7.2，则此文件会定义7.2版本新特性函数供低版本的 PHP 使用

`vendor/symfony/var-dumper/Resources/functions/dump.php`: 传说中 dump 和 dd 函数就在这里面哦

`vendor/symfony/polyfill-ctype/bootstrap.php`: 如果 PHP 内置没有ctype 系列函数， 则定义的这些函数

`vendor/swiftmailer/swiftmailer/lib/swift_required.php`: 注册了 Swift 相关类的自动加载函数

`vendor/laravel/framework/src/Illuminate/Foundation/helpers.php`: Laravel 的相关辅助函数

`vendor/laravel/framework/src/Illuminate/Support/helpers.php`: 也是 Laravel 的相关辅助函数

`vendor/myclabs/deep-copy/src/DeepCopy/deep_copy.php`: 定义一个深度拷贝功能的一个函数，执行此函数返回是深度拷贝的相关服务对象

`vendor/psy/psysh/src/functions.php`: psy 相关的一些辅助函数

## 总结

- Composer 自动加载的入口文件 `vendor\autoload.php`

  所有使用 Composer 自动加载功能大门。

- 真 • Composer 自动加载入口文件 `vendor\composer\autoload_real.php`
  
  Composer 内部执行的入口文件，这个文件相当于加工者。

  它将 **自动加载映射类** 和 **核心自动加载类** 保存在自己身体里，通过逻辑操作，将 **自动加载映射类** 定义的 `类全名 => 类绝对路径` 赋值到 **核心自动加载类** 中。

  然后执行一项重要操作：调用 **核心自动加载类** 中的 register 函数，将 loadClass 方法添加 PHP 自动加载序列中，以供整个 Laravel 类的自动加载需求。

  再然后通过 自动加载映射类 定义的全局函数路径，加载 Laravel 所需全局函数。

  最后返回 **核心自动加载类**。

- Composer 自动加载映射类 `vendor\composer\autoload_static.php`

  它主要定义了 `全局函数标识码 => 函数文件所在路径` 以及 `类全名 => 类绝对路径` 的有关映射，类型为数组。

  同时还定义了一个闭包，用来将这些映射赋值到 **核心自动加载类** 中

- Composer 核心自动加载类 `vendor\composer\ClassLoader.php`

  实现 类自动加载方法注册 和 根据类名查询类所在绝对路径 的功能

  实现整个 Laravel 类加载时所需的自动加载方法