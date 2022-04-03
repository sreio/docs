## 简介

> 本篇主要讲解 Laravel 服务容器中 make 方法主要作用

## 正文

下面是 `public/index.php` 入口文件的一段代码，相信大家都见过

```php
$kernel = $app->make(Illuminate\Contracts\Http\Kernel::class);
```

它作用解析出在服务容器 `bindings` 属性中以 `'Illuminate\Contracts\Http\Kernel'` 为键，对应的服务对象。据上一章我们知道是 `'App\Http\Kernel'` 的类对象

> 注意：以 `'Illuminate\Contracts\Http\Kernel'` 为键，实际对应不是 `'App\Http\Kernel'` 的类对象，而是对应能够生成 `'App\Http\Kernel'` 类对象的闭包函数。

那么他是如何解析出的呢，我们看一下源码

```php
public function make($abstract, array $parameters = [])
{
    return $this->resolve($abstract, $parameters);
}
```

实际调用了 resolve 方法，我们看一下 resolve 方法。

看下面这方法，是不是头大，这么多，其实核心的就那么几个地方，我已标注

```php
protected function resolve($abstract, $parameters = [])
{
    $abstract = $this->getAlias($abstract);

    $needsContextualBuild = ! empty($parameters) || ! is_null(
        $this->getContextualConcrete($abstract)
    );
	
    // ★核心一★：检测之前是否解析过对应的对象，有则返回对应对象；一种缓存机制，防止重复解析，提高程序运行效率
    if (isset($this->instances[$abstract]) && ! $needsContextualBuild) {
        return $this->instances[$abstract];
    }

    $this->with[] = $parameters;
    
    // ★核心二★：通过 $abstract 到 bindings 属性中取得生成对象的闭包函数，没有则返回 $abstract 本身
    $concrete = $this->getConcrete($abstract);
	
    // ★核心三★：判断是否可以创建对象，来确定调用 build 方法还是重新调用 make 方法
    if ($this->isBuildable($concrete, $abstract)) {
        $object = $this->build($concrete);
    } else {
        $object = $this->make($concrete);
    }
	
    foreach ($this->getExtenders($abstract) as $extender) {
        $object = $extender($object, $this);
    }
	
    if ($this->isShared($abstract) && ! $needsContextualBuild) {
        $this->instances[$abstract] = $object;
    }

    $this->fireResolvingCallbacks($abstract, $object);
	
    $this->resolved[$abstract] = true;

    array_pop($this->with);

    return $object;
}
```

由于第一次执行，核心一忽略。当以 `'Illuminate\Contracts\Http\Kernel'` 为 $abstract 时，获取的闭包函数定义的位置如下

```php
protected function getClosure($abstract, $concrete)
{
    return function ($container, $parameters = []) use ($abstract, $concrete) {
        if ($abstract == $concrete) {
            return $container->build($concrete);
        }

        return $container->make($concrete, $parameters);
    };
}
```

会执行上面代码 function 里面的内容，因为 `Illuminate\Contracts\Http\Kernel` 不等于 `App\Http\Kernel` ，故以 `App\Http\Kernel` 为 $abstract 重新调用了 make 方法

此时的 make 方法可以执行到 build 方法，我们来看一下

```php
public function build($concrete)
{
	// 首先判断 concrete 是不是闭包，如果是就会执行
    if ($concrete instanceof Closure) {
        return $concrete($this, $this->getLastParameterOverride());
    }
	
	// PHP 反射类操作
    $reflector = new ReflectionClass($concrete);
		
    if (! $reflector->isInstantiable()) {
        return $this->notInstantiable($concrete);
    }

    $this->buildStack[] = $concrete;
	
	// 获取 concrete 类的构造方法
    $constructor = $reflector->getConstructor();
	
    if (is_null($constructor)) {
        array_pop($this->buildStack);

        return new $concrete;
    }
	
	// 获取 concrete 类的构造方法上的参数
    $dependencies = $constructor->getParameters();
	
	// 根据参数的类型约束，注入依赖
    $instances = $this->resolveDependencies(
        $dependencies
    );

    array_pop($this->buildStack);

    return $reflector->newInstanceArgs($instances);
}
```

此时的 $concrete 等于 `'App\Http\Kernel'`，会调用 PHP 内置的反射类，对 `App\Http\Kernel` 进行处理。

## 关于 ReflectionClass

[官方文档](http://www.php.net/manual/zh/class.reflectionclass.php)

ReflectionClass：我的理解是，这是一个对类进行处理的对象，它甚至能够获取类中的注释，执行类中的私有方法；`getConstructor` 返回一个 `ReflectionMethod` 对象，它可以对类中方法进行更进一步处理和控制，比如获取方法调用时的参数列表，包含参数的类型约束，这点很重要，因为它是 Laravel 实现依赖注入的手段；反射方法对象中的 `getParameters` 将返回一个 `ReflectionParameter ` 对象，它里面就包含了类型约束和参数名称。

```php
$instances = $this->resolveDependencies(
    $dependencies
);
```

看到上面这段代码了吗，`resolveDependencies` 方法，就是解析出要类构造方法中的约束类，通过多次 make 自动获取所依赖的对象，然后通过反射类的 `newInstanceArgs` 实例化

最终获取了 `App\Http\Kernel` 类的对象，并且仅仅通过一行字符串，其中构造函数所依赖的对象，全部由 make 自动处理，用户可以不再管理那个依赖这个依赖了，不仅变得方便、变得解耦，还变得功能健全