## 简介

> 本篇主要对 Laravel Request 对象的初始化进行讲解，它首先从 SymfonyRequest 中获取 Request 对象，然后根据 Laravel 需要对其进行修改

## 正文

先看一段入口文件的代码：

```php
$response = $kernel->handle(
    $request = Illuminate\Http\Request::capture()
);
```

相信同僚们，都很清楚这个段代码是干什么的。套用官方文档的一句话，它就是整个 Laravel 生产 `response` 对象数据的大黑盒子，材料就是 `request` 对象。

`$kernel` 通过上一章，我们知道是 `App\Http\Kernel` 类的实例对象，此类继承自 `Illuminate\Foundation\Http\Kernel` ，看图：

![file](https://cdn.learnku.com/uploads/images/201809/27/27709/6wiCwb6zzT.png?imageView2/2/w/1240/h/0)

## 关于 `Kernel` 类中的 `handle` 方法

对于这个方法的简要说明，官方讲的很清楚了，具体执行，以后章节会一点点讲，非常多呀。。。

[官方关于 handle 方法的简要说明](https://learnku.com/docs/laravel/5.6/lifecycle/1358)

## 今天的重点 `SymfonyRequest`

我们再看一下入口文件的这段代码。

```php
$response = $kernel->handle(
    $request = Illuminate\Http\Request::capture()
);
```

大家看到没 `handle` 方法的参数是 `Illuminate\Http\Request::capture()` 静态方法的返回值

那，我们看一下这个静态方法。

```php
public static function capture()
{
    static::enableHttpMethodParameterOverride();

    return static::createFromBase(SymfonyRequest::createFromGlobals());
}
```

`static::enableHttpMethodParameterOverride();` 这行指启动方法重载，能够在前端通过 `{{method_field('PUT')}} ` 伪造一个 PUT 或者 DELETE 请求。

第二行的 `createFromBase` 方法的参数，就是 `Symfony` 组件有关请求参数处理的返回。

```php
public static function createFromGlobals()
{
    $request = self::createRequestFromFactory($_GET, $_POST, array(), $_COOKIE, $_FILES, $_SERVER);

    if (0 === strpos($request->headers->get('CONTENT_TYPE'), 'application/x-www-form-urlencoded')
        && in_array(strtoupper($request->server->get('REQUEST_METHOD', 'GET')), array('PUT', 'DELETE', 'PATCH'))
       ) {
        parse_str($request->getContent(), $data);
        $request->request = new ParameterBag($data);
    }

    return $request;
}
```

上面就是 `SymfonyRequest::createFromGlobals()` 静态方法的代码，我们可以看到，通过 `createRequestFromFactory` 静态方法处理了 PHP 预定义全局变量 `$_GET` 等前端发送过来的所有数据。这个处理就是实例化当前类，将 PHP 这些预定义变量当做参数，执行构造函数，并返回实例化的对象。

接下对特殊请求方法（PUT、DELETE、PATCH）做初始化时的有关数据操作

最后返回 `$request`

接下来，我们看一下 `createRequestFromFactory` 方法

```php
private static function createRequestFromFactory(array $query = array(), array $request = array(), array $attributes = array(), array $cookies = array(), array $files = array(), array $server = array(), $content = null)
{
    if (self::$requestFactory) {
        $request = call_user_func(self::$requestFactory, $query, $request, $attributes, $cookies, $files, $server, $content);

        if (!$request instanceof self) {
            throw new \LogicException('The Request factory must return an instance of Symfony\Component\HttpFoundation\Request.');
        }

        return $request;
    }

    return new static($query, $request, $attributes, $cookies, $files, $server, $content);
}
```

`self::$requestFactory` 为 `null` 不执行 if 里面的代码，最后一行，看到没， `new Static(...)` ，即实例化当前类，把 PHP 的预定义变量传入

接下来我们看一下它的构造函数

```php
public function __construct(array $query = array(), array $request = array(), array $attributes = array(), array $cookies = array(), array $files = array(), array $server = array(), $content = null)
{
    $this->initialize($query, $request, $attributes, $cookies, $files, $server, $content);
}
```

调用了 `initialize` 方法，同样参数不变。

说一下这几个参数

`$query`：PHP 的 `$_GET` 数组

`$request`： PHP 的 `$_POST` 数组

`$attributes`： 其它属性参数，现在是空数组，以后可能要往里添数据

`$cookies`： PHP 的 `$_COOKIE` 数组

`$files`： PHP 的 `$_FILES` 数组

`$server`： PHP 的 `$_SERVER` 数组

好了，我们看一下 `intialize` 方法：

```php
public function initialize(array $query = array(), array $request = array(), array $attributes = array(), array $cookies = array(), array $files = array(), array $server = array(), $content = null)
{
    $this->request = new ParameterBag($request);
    $this->query = new ParameterBag($query);
    $this->attributes = new ParameterBag($attributes);
    $this->cookies = new ParameterBag($cookies);
    $this->files = new FileBag($files);
    $this->server = new ServerBag($server);
    $this->headers = new HeaderBag($this->server->getHeaders());

    $this->content = $content;
    $this->languages = null;
    $this->charsets = null;
    $this->encodings = null;
    $this->acceptableContentTypes = null;
    $this->pathInfo = null;
    $this->requestUri = null;
    $this->baseUrl = null;
    $this->basePath = null;
    $this->method = null;
    $this->format = null;
}
```

初始化，就是给一堆属性赋值，除了 `files` 、`server` 、`headers` 参数赋予特殊处理对象，其它都是普通参数处理对象（`ParameterBag` 类的实例）

看一下 `ParameterBag` 类的构造函数

```php
public function __construct(array $parameters = array())
{
    $this->parameters = $parameters;
}
```

就是一个简单属性赋值

现在看一下 `$this->server->getHeaders()` 的参数是如何从 `server` 挑选出 `headers` 的

```php
public function getHeaders()
{
    $headers = array();
    $contentHeaders = array('CONTENT_LENGTH' => true, 'CONTENT_MD5' => true, 'CONTENT_TYPE' => true);
    foreach ($this->parameters as $key => $value) {
        if (0 === strpos($key, 'HTTP_')) {
            $headers[substr($key, 5)] = $value;
        }
        // CONTENT_* are not prefixed with HTTP_
        elseif (isset($contentHeaders[$key])) {
            $headers[$key] = $value;
        }
    }

    if (isset($this->parameters['PHP_AUTH_USER'])) {
        $headers['PHP_AUTH_USER'] = $this->parameters['PHP_AUTH_USER'];
        $headers['PHP_AUTH_PW'] = isset($this->parameters['PHP_AUTH_PW']) ? $this->parameters['PHP_AUTH_PW'] : '';
    } else {
        $authorizationHeader = null;
        if (isset($this->parameters['HTTP_AUTHORIZATION'])) {
            $authorizationHeader = $this->parameters['HTTP_AUTHORIZATION'];
        } elseif (isset($this->parameters['REDIRECT_HTTP_AUTHORIZATION'])) {
            $authorizationHeader = $this->parameters['REDIRECT_HTTP_AUTHORIZATION'];
        }

        if (null !== $authorizationHeader) {
            if (0 === stripos($authorizationHeader, 'basic ')) {                
                $exploded = explode(':', base64_decode(substr($authorizationHeader, 6)), 2);
                if (2 == count($exploded)) {
                    list($headers['PHP_AUTH_USER'], $headers['PHP_AUTH_PW']) = $exploded;
                }
            } elseif (empty($this->parameters['PHP_AUTH_DIGEST']) && (0 === stripos($authorizationHeader, 'digest '))) {                
                $headers['PHP_AUTH_DIGEST'] = $authorizationHeader;
                $this->parameters['PHP_AUTH_DIGEST'] = $authorizationHeader;
            } elseif (0 === stripos($authorizationHeader, 'bearer ')) {
                $headers['AUTHORIZATION'] = $authorizationHeader;
            }
        }
    }

    if (isset($headers['AUTHORIZATION'])) {
        return $headers;
    }
   
    if (isset($headers['PHP_AUTH_USER'])) {
        $headers['AUTHORIZATION'] = 'Basic '.base64_encode($headers['PHP_AUTH_USER'].':'.$headers['PHP_AUTH_PW']);
    } elseif (isset($headers['PHP_AUTH_DIGEST'])) {
        $headers['AUTHORIZATION'] = $headers['PHP_AUTH_DIGEST'];
    }

    return $headers;
}
```

首先 `foreach` 循环，从 `server` 中取出 `HTTP_` 开头和 `CONTENT_` 有关的键值对，放入 `headers` 中；然后，取关于授权登录方面的头信息，如果 `server` 中有 `PHP_AUTH_USER` ，则将 `PHP_AUTH_USER` 和 `PHP_AUTH_PW` 取出放入 `headers`中；如果没有 `PHP_AUTH_USER` ，则在 `server` 中寻找关于 `HTTP_AUTHORIZATION`、`REDIRECT_HTTP_AUTHORIZATION`、`AUTHORIZATION` 的键值对；最后对授权内容进行相关字符串的变换处理。



最后，再经过 `Laravel` 的 `Request` 类，对生成好的 `Symfony Request` 对象进行处理；生成了一个 `Laravel` 类的副本，将 `PHP` 的预定义变量，赋值到这个副本的属性中，基本和 `Symfony Request` 实例化的赋值一样，之后，对 `json` 进行相关处理；还有 `Laravel` 的 `Request` 类继承自 `Symfony` 的 `Request` 类。

现在，我们到 `handle` 函数中，看一下生成好的 `Request` 对象是什么样子的：

![file](https://cdn.learnku.com/uploads/images/201809/27/27709/Rhp7sWsU7D.png?imageView2/2/w/1240/h/0)

这是 `request 对象` 的变量详情：

```php
Illuminate\Http\Request::__set_state(array(
   'json' => NULL,
   'convertedFiles' => NULL,
   'userResolver' => NULL,
   'routeResolver' => NULL,
   'attributes' => 
  Symfony\Component\HttpFoundation\ParameterBag::__set_state(array(
     'parameters' => 
    array (
    ),
  )),
   'request' => 
  Symfony\Component\HttpFoundation\ParameterBag::__set_state(array(
     'parameters' => 
    array (
      'XDEBUG_SESSION_START' => '12084',
    ),
  )),
   'query' => 
  Symfony\Component\HttpFoundation\ParameterBag::__set_state(array(
     'parameters' => 
    array (
      'XDEBUG_SESSION_START' => '12084',
    ),
  )),
   'server' => 
  Symfony\Component\HttpFoundation\ServerBag::__set_state(array(
     'parameters' => 
    array (
      'ALLUSERSPROFILE' => 'C:\\ProgramData',
      'ANDROID_HOME' => 'C:\\Users\\Administrator\\AppData\\Local\\Android\\Sdk',
      'APPDATA' => 'C:\\Users\\Administrator\\AppData\\Roaming',
      'CommonProgramFiles' => 'C:\\Program Files\\Common Files',
      'CommonProgramFiles(x86)' => 'C:\\Program Files (x86)\\Common Files',
      'CommonProgramW6432' => 'C:\\Program Files\\Common Files',
      'COMPUTERNAME' => 'ICOS-20180531MV',
      'ComSpec' => 'C:\\windows\\system32\\cmd.exe',
      'FPS_BROWSER_APP_PROFILE_STRING' => 'Internet Explorer',
      'FPS_BROWSER_USER_PROFILE_STRING' => 'Default',
      'HOMEDRIVE' => 'C:',
      'HOMEPATH' => '\\Users\\Administrator',
      'LOCALAPPDATA' => 'C:\\Users\\Administrator\\AppData\\Local',
      'LOGONSERVER' => '\\\\ICOS-20180531MV',
      'NUMBER_OF_PROCESSORS' => '8',
      'OS' => 'Windows_NT',
      'Path' => 'C:\\Program Files (x86)\\Common Files\\Oracle\\Java\\javapath;C:\\windows\\system32;C:\\windows;C:\\windows\\System32\\Wbem;C:\\windows\\System32\\WindowsPowerShell\\v1.0\\;C:\\Program Files (x86)\\NVIDIA Corporation\\PhysX\\Common;C:\\Program Files\\Git\\cmd;D:\\Server\\Shell;C:\\Program Files\\Sublime Text 3;D:\\Server\\Nginx;D:\\Server\\PHP;C:\\Program Files\\Redis\\;C:\\Program Files\\nodejs\\;C:\\Python27;C:\\Users\\Administrator\\AppData\\Local\\Android\\Sdk\\platform-tools;D:\\Server\\MySQL\\bin;C:\\Users\\Administrator\\AppData\\Local\\Microsoft\\WindowsApps;C:\\Users\\Administrator\\AppData\\Roaming\\npm',
      'PATHEXT' => '.COM;.EXE;.BAT;.CMD;.VBS;.VBE;.JS;.JSE;.WSF;.WSH;.MSC;.CPL',
      'PROCESSOR_ARCHITECTURE' => 'AMD64',
      'PROCESSOR_IDENTIFIER' => 'Intel64 Family 6 Model 142 Stepping 10, GenuineIntel',
      'PROCESSOR_LEVEL' => '6',
      'PROCESSOR_REVISION' => '8e0a',
      'ProgramData' => 'C:\\ProgramData',
      'ProgramFiles' => 'C:\\Program Files',
      'ProgramFiles(x86)' => 'C:\\Program Files (x86)',
      'ProgramW6432' => 'C:\\Program Files',
      'PROMPT' => '$P$G',
      'PSModulePath' => 'C:\\Users\\Administrator\\Documents\\WindowsPowerShell\\Modules;C:\\Program Files\\WindowsPowerShell\\Modules;C:\\windows\\system32\\WindowsPowerShell\\v1.0\\Modules',
      'PUBLIC' => 'C:\\Users\\Public',
      'SESSIONNAME' => 'Console',
      'SystemDrive' => 'C:',
      'SystemRoot' => 'C:\\windows',
      'TEMP' => 'C:\\Users\\ADMINI~1\\AppData\\Local\\Temp',
      'TMP' => 'C:\\Users\\ADMINI~1\\AppData\\Local\\Temp',
      'USERDOMAIN' => 'ICOS-20180531MV',
      'USERDOMAIN_ROAMINGPROFILE' => 'ICOS-20180531MV',
      'USERNAME' => 'Administrator',
      'USERPROFILE' => 'C:\\Users\\Administrator',
      'windir' => 'C:\\windows',
      'HTTP_ACCEPT_LANGUAGE' => 'zh-CN,zh;q=0.9,en-US;q=0.8,en;q=0.7',
      'HTTP_ACCEPT_ENCODING' => 'gzip, deflate, br',
      'HTTP_ACCEPT' => 'text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng;q=0.8',
      'HTTP_USER_AGENT' => 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.106 Safari/537.36',
      'HTTP_UPGRADE_INSECURE_REQUESTS' => '1',
      'HTTP_CONNECTION' => 'keep-alive',
      'HTTP_HOST' => '127.0.0.1',
      'REDIRECT_STATUS' => '200',
      'SERVER_NAME' => 'localhost',
      'SERVER_PORT' => '80',
      'SERVER_ADDR' => '127.0.0.1',
      'REMOTE_PORT' => '50070',
      'REMOTE_ADDR' => '127.0.0.1',
      'SERVER_SOFTWARE' => 'nginx/1.14.0',
      'GATEWAY_INTERFACE' => 'CGI/1.1',
      'REQUEST_SCHEME' => 'http',
      'SERVER_PROTOCOL' => 'HTTP/1.1',
      'DOCUMENT_ROOT' => 'D:/www/czRobotLogicServer/public',
      'DOCUMENT_URI' => '/index.php',
      'REQUEST_URI' => '/admin/login?XDEBUG_SESSION_START=12084',
      'SCRIPT_NAME' => '/index.php',
      'CONTENT_LENGTH' => '',
      'CONTENT_TYPE' => '',
      'REQUEST_METHOD' => 'GET',
      'QUERY_STRING' => 'XDEBUG_SESSION_START=12084',
      'SCRIPT_FILENAME' => 'D:/www/czRobotLogicServer/public/index.php',
      'FCGI_ROLE' => 'RESPONDER',
      'PHP_SELF' => '/index.php',
      'REQUEST_TIME_FLOAT' => 1536800963.612386,
      'REQUEST_TIME' => 1536800963,
    ),
  )),
   'files' => 
  Symfony\Component\HttpFoundation\FileBag::__set_state(array(
     'parameters' => 
    array (
    ),
  )),
   'cookies' => 
  Symfony\Component\HttpFoundation\ParameterBag::__set_state(array(
     'parameters' => 
    array (
    ),
  )),
   'headers' => 
  Symfony\Component\HttpFoundation\HeaderBag::__set_state(array(
     'headers' => 
    array (
      'accept-language' => 
      array (
        0 => 'zh-CN,zh;q=0.9,en-US;q=0.8,en;q=0.7',
      ),
      'accept-encoding' => 
      array (
        0 => 'gzip, deflate, br',
      ),
      'accept' => 
      array (
        0 => 'text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8',
      ),
      'user-agent' => 
      array (
        0 => 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.106 Safari/537.36',
      ),
      'upgrade-insecure-requests' => 
      array (
        0 => '1',
      ),
      'connection' => 
      array (
        0 => 'keep-alive',
      ),
      'host' => 
      array (
        0 => '127.0.0.1',
      ),
      'content-length' => 
      array (
        0 => '',
      ),
      'content-type' => 
      array (
        0 => '',
      ),
    ),
     'cacheControl' => 
    array (
    ),
  )),
   'content' => NULL,
   'languages' => NULL,
   'charsets' => NULL,
   'encodings' => NULL,
   'acceptableContentTypes' => NULL,
   'pathInfo' => NULL,
   'requestUri' => NULL,
   'baseUrl' => NULL,
   'basePath' => NULL,
   'method' => NULL,
   'format' => NULL,
   'session' => NULL,
   'locale' => NULL,
   'defaultLocale' => 'en',
   'isHostValid' => true,
   'isForwardedValid' => true,
))
```

