## 一、什么是 Cookie ?
    Cookie 是一段不超过 4KB 的 小型文本信息，是网站为了辨别用户身份，而 存储在用户本地终端上的数据。

### 1.1 Cookie 由哪几部分组成?
  ![img](./img/cookie.png ':size=90%')

 - Cookie 由 `key:value`、`Expires属性`、`Path属性`、`Domain属性`、`Secure属性`、`HTTPOnly 属性` 组成。

    - `key:value` : 设置 Cookie 的名称及相对应的值。

    - `Expires属性` : 设置 Cookie 的生存周期。

        - 生存周期有两种类型 : `会话性` 与 `持久性` 。
            1. `会话性 : 不设置 cookie 过期时间` 时为 `会话性 Cookie`，仅保存在浏览器内存中，并在用户关闭浏览器时失效。比如: 用户登录之后，此时用户信息 Cookie 保存在浏览器中，当用户关掉浏览器后，再次打开网站就需要再次登录。
            2. `持久性 : 设置 Cookie 过期时间` 时为 `持久性 Cookie`，持久性 Cookie 会保存在用户硬盘中，直到 Cookie 过期或主动退出(清除 Cookie)时才会失效。

    - `Path属性` : 规定 Cookie 的服务器路径。

        - 如果路径设置为 “/”，那么 Cookie 将在整个域名内有效.如果路径设置为 “/test/”，那么 Cookie 将在 test 目录下及其所有子目录下有效。默认的路径值是 Cookie 所处的当前目录。

    - `Domain属性` : 指定了可以访问该 Cookie 的 Web 站点或域。

        - 为了让 Cookie 在 example.com 的所有子域名中有效，您需要把 Cookie 的域名设置为 “.example.com”。当您把 Cookie 的域名设置为 www.example.com 时，Cookie 仅在 www 子域名中有效。

    - `Secure属性` : 指定是否使用HTTPS安全协议发送 Cookie。

    - `HTTPOnly 属性` : 用于防止客户端脚本通过 document.cookie 属性访问 Cookie，有助于保护 Cookie 不被跨站脚本攻击窃取或篡改。

### 1.2 Cookie 有什么作用？
    Cookie 用来跟踪用户身份，进行会话控制。因为 HTTP 是无状态协议，一次请求后，客户端与服务器就会关闭连接，以后用户再发起请求时，服务器不知道是哪个用户请求他，这时就用到 Cookie 了。

### 1.3 Cookie 的工作原理
    例如: 用户登录

        1. 客户端浏览器发起请求 ( 用户打开网站，输入账号密码，点击登录)
        2. 服务器接收并响应 ( 服务器接收请求，创建 Cookie，将用户信息存入，并将 Cookie 返回客户端浏览器，浏览器将 Cookie 保存至内存或本地文件)
        3. 客户端之后再发起请求 ( 将 Cookie 一起发给服务器）
        4. 服务器接收并响应 ( 服务器收到 Cookie 后，可以识别是哪位用户进行操作，再进行处理)

### 1.4 PHP 设置 Cookie
```php
// 语法
setcookie(name, value, expire, path, domain, secure);

$value = 'cookie value'; // Cookie 值

// 会话性 cookie
setcookie("testCookie", $value);

// 1小时过期的 cookie
setcookie("testCookie", $value, time()+3600);

// 删除 cookie， 将过期时间设置以过去时间
setcookie("testCookie", '', time()-1);

//-------------------------------------------

// PHP 获取 cookie
$testCookie = $_COOKIE['testCookie'];
```

## 二、 什么是Session
    Session 用来跟踪用户身份，进行会话控制或保持会话。

### 2.1 Session 有什么作用？
    Session 用来跟踪用户身份，进行会话控制或保持会话

### 2.2 Session 的工作原理
    客户端浏览器打开 cookie 时

        1. 客户端浏览器发起请求 ( 用户打开网站，输入账号密码，点击登录)
        2. 服务器接收并响应
            服务器接收请求，开启 session session_start()，并随机生成唯一的32位的 session_id，创建以 session_id 命名的文件用来保存用户信息。通过 HTTP 响应头将 session_id 返回客户端浏览器，浏览器将 session_id 通过 cookie 保存至内存或本地文件中)

        3. 客户端之后再发起请求 ( 将 cookie 中的 session_id 一起发给服务器）
        4. 服务器接收并响应 ( 服务器收到 session_id 后，去寻找 session 文件中获取用户信息，识别是哪位用户进行操作，再进行处理)

### 2.3 PHP 设置 Session
    在 php.ini 中 session.save_path 配置项找到 session 默认保存路径

```php
// 初始化
session_start();

// 设置 session
$_SESSION['name'] = "Dom";

// 删除 session
if (isset($_SESSION['name'])) {
    unset($_SESSION['name']);
}
```

## 三、 Cookie 与 Session 有什么区别？

### 1. 存储位置不同
    Cookie 存储在浏览器内存或者客户端的硬盘中。

    Session 存储在服务器的硬盘或数据库或缓存或等等中，但如果使用 cookie 保存 session_id 时，session 会依赖 cookie。 session 生成的 session_id 会返给客户端，客户端将 session_id 用 cookie 进行保存。

### 2. 存储大小不同
    cookie 存储大小 4k 左右, 不同浏览器限制 单个域名存储 cookie 的数量 也不一样。1个字母占1个字节，1个汉字占3个字节。

    session 不限制大小，只限制生命周期。

### 3. 性能程度不同
    cookie 存储在浏览器或客户端本地文件中，访问速度快。

    session 如果存储在服务器上，如果在某个时间段，访问量暴增，会占用服务器性能。

### 4. 安全程度不同
    session 相对 cookie ，session 比较安全。

    防君子不防小人

## 四、 浏览器禁用 cookie 后, 如何使用 session

### 第一种: 通过 url 传值
```php
# 通过 url 重写
https://www.baidu.com?sessid=123456
```

### 第二种：通过隐藏表单或 ajax 传值
```htm
<!-- html 隐藏表单 -->

<form name='testSID' action='/???'>
    <input type='hidden' name='sessid' value='123456'>
</form>
```

### 第三种: 修改 php.ini 中的 use_trans_sid
    将 php.ini 中的 use_trans_sid=0 修改为 use_trans_sid=1, 就会检查客户端是否禁用 cookie，如果禁用，就会默认采用 第一种: 通过 url 传值 方式进行传递 sessionid
