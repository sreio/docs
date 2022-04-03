## 简介

其根本，就是 `Illuminate\Foundation\Application` 类的一个实例化对象。

其作用之一，就是把 Laravel 各个零散的服务对象（如数据库服务对象、Redis 服务对象、路由服务对象等）通过 `key=>value` 的形式赋值给服务容器中的属性（此属性的值是数组类型）；而 key 可以是一个字符串，可能是类全名（包含命名空间），也可能是一个简短的英语单词（就是类的别名）；而 value 就可能是类全名（字段串），可能是数组、可能是对象。以上这种作用，就叫注册或者叫绑定

## 关于 bind方法

Laravel 服务容器继承了 `Illuminate\Container\Container`  类，含有 `bindings` 属性，具体情形请看下图

![file](https://cdn.learnku.com/uploads/images/201809/27/27709/ycOEed8gFO.png?imageView2/2/w/1240/h/0)

大家再看一下服务容器中的 bind 方法

![file](https://cdn.learnku.com/uploads/images/201809/27/27709/EypoogN67P.png?imageView2/2/w/1240/h/0)

看到 bind 方法中的这行代码了吗，如下

```php
$this->bindings[$abstract] = compact('concrete', 'shared');
```

compact 函数是 PHP 内置的数组处理函数，意思是将变量名作为键，变量值作为值，合并的一个数组中，并返回，而这些变量必须在当前作用域中可用

上面这行代码，已经很清晰的说明了 bind 方法的核心作用，就是以 `$abstract`  的值为键（其值为 bind 的第一个参数），`$concrete` 和 `$shared`  组成的关联数组为值，赋值给 `bindings` 属性。

## 关于 singleton 方法

相信有的朋友对 `bootstrap/app.php`  中这几段代码有疑惑

```php
$app->singleton(
    Illuminate\Contracts\Http\Kernel::class,
    App\Http\Kernel::class
);

$app->singleton(
    Illuminate\Contracts\Console\Kernel::class,
    App\Console\Kernel::class
);

$app->singleton(
    Illuminate\Contracts\Debug\ExceptionHandler::class,
    App\Exceptions\Handler::class
);
```

它们代表什么意思呢，先说 `类名::class`  的作用：它的作用是返回一个标准的类全名（包含从根命名空间到当前空间的路由）

```php
$app->singleton(
    Illuminate\Contracts\Http\Kernel::class,
    App\Http\Kernel::class
);
```

等于：

```php
$app->singleton(
    'Illuminate\Contracts\Http\Kernel',
    'App\Http\Kernel'
);
```

看一下服务容器中的 singleton 方法

```php
public function singleton($abstract, $concrete = null)
{
    $this->bind($abstract, $concrete, true);
}
```

大家看到没，实际就是调用了服务容器中的 bind 方法，将 `$abstract: 'Illuminate\Contracts\Http\Kernel'`、`$concrete: 'App\Http\Kernel'` 、`$share: true` 作为参数



这里有一点，就是当 `$concrete`  是类全名（字符串）时，将在 bind 方法中执行以下代码，来获取 闭包对象

```php
if (! $concrete instanceof Closure) {
    $concrete = $this->getClosure($abstract, $concrete);
}
```

其中 `getClosure` 将返回一个 function 函数，即 PHP 的闭包对象，关于闭包对象，请看第三章的相关内容

最后我们看一下执行完 `bootstrap/app.php`  中三个 singleton 方法后的容器 `bindings` 属性的变量情况

![file](https://cdn.learnku.com/uploads/images/201809/27/27709/FQ1Z5Pkzog.png?imageView2/2/w/1240/h/0)

