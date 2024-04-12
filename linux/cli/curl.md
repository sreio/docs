> [curl](https://curl.haxx.se/docs/manpage.html) 是一个用来发送网络请求的工具命令， 在 网络服务开发 以及 网络调试 中特别有用。 它支持的协议包括：

- FTP
- FTPS
- HTTP
- HTTPS
- POP3
- POP3S
- SMTP
- SMTPS
- etc

本文以 HTTP 协议为例，介绍 curl 命令的操作方式，及其在 Web 开发中的应用。

## 发起请求

给定一个 URL ， curl 对其发起请求并将响应数据输出到标准输出：

```bash
$ curl www.baidu.com
<!DOCTYPE html>
<!--STATUS OK--><html> <head><meta http-equiv=content-type content=text/html;charset=utf-8><meta http-equiv=X-UA-Compatible content=IE=Edge><meta content=always name=referrer><link rel=stylesheet type=text/css href=http://s1.bdstatic.com/r/www/cache/bdorz/baidu.min.css><title>百度一下，你就知道</title></head> <body link=#0000cc> <div id=wrapper> <div id=head> <div class=head_wrapper> <div class=s_form> <div class=s_form_wrapper> <div id=lg> <img hidefocus=true src=//www.baidu.com/img/bd_logo1.png width=270 height=129> </div> <form id=form name=f action=//www.baidu.com/s class=fm> <input type=hidden name=bdorz_come value=1> <input type=hidden name=ie value=utf-8> <input type=hidden name=f value=8> <input type=hidden name=rsv_bp value=1> <input type=hidden name=rsv_idx value=1> <input type=hidden name=tn value=baidu><span class="bg s_ipt_wr"><input id=kw name=wd class=s_ipt value maxlength=255 autocomplete=off autofocus></span><span class="bg s_btn_wr"><input type=submit id=su value=百度一下 class="bg s_btn"></span> </form> </div> </div> <div id=u1> <a href=http://news.baidu.com name=tj_trnews class=mnav>新闻</a> <a href=http://www.hao123.com name=tj_trhao123 class=mnav>hao123</a> <a href=http://map.baidu.com name=tj_trmap class=mnav>地图</a> <a href=http://v.baidu.com name=tj_trvideo class=mnav>视频</a> <a href=http://tieba.baidu.com name=tj_trtieba class=mnav>贴吧</a> <noscript> <a href=http://www.baidu.com/bdorz/login.gif?login&amp;tpl=mn&amp;u=http%3A%2F%2Fwww.baidu.com%2f%3fbdorz_come%3d1 name=tj_login class=lb>登录</a> </noscript> <script>document.write('<a href="http://www.baidu.com/bdorz/login.gif?login&tpl=mn&u='+ encodeURIComponent(window.location.href+ (window.location.search === "" ? "?" : "&")+ "bdorz_come=1")+ '" name="tj_login" class="lb">登录</a>');</script> <a href=//www.baidu.com/more/ name=tj_briicon class=bri style="display: block;">更多产品</a> </div> </div> </div> <div id=ftCon> <div id=ftConw> <p id=lh> <a href=http://home.baidu.com>关于百度</a> <a href=http://ir.baidu.com>About Baidu</a> </p> <p id=cp>&copy;2017&nbsp;Baidu&nbsp;<a href=http://www.baidu.com/duty/>使用百度前必读</a>&nbsp; <a href=http://jianyi.baidu.com/ class=cp-feedback>意见反馈</a>&nbsp;京ICP证030173号&nbsp; <img src=//www.baidu.com/img/gs.gif> </p> </div> </div> </div> </body> </html>
```

例子中，我们对百度的网址发起请求，得到的是其首页的源码( HTML )。

## 显示头部

调试 Web 服务时，我们经常需要查看 HTTP 响应（ response ）的头部信息，加上 -i 选项即可：

```bash
$ curl -i www.baidu.com
HTTP/1.1 200 OK
Accept-Ranges: bytes
Cache-Control: private, no-cache, no-store, proxy-revalidate, no-transform
Connection: Keep-Alive
Content-Length: 2381
Content-Type: text/html
Date: Wed, 20 Jun 2018 00:42:10 GMT
Etag: "588604dd-94d"
Last-Modified: Mon, 23 Jan 2017 13:27:57 GMT
Pragma: no-cache
Server: bfe/1.0.8.18
Set-Cookie: BDORZ=27315; max-age=86400; domain=.baidu.com; path=/

<!DOCTYPE html>
<!--STATUS OK--><html> <head><meta http-equiv=content-type content=text/html;charset=utf-8><meta http-equiv=X-UA-Compatible content=IE=Edge><meta content=always name=referrer><link rel=stylesheet type=text/css href=http://s1.bdstatic.com/r/www/cache/bdorz/baidu.min.css><title>百度一下，你就知道</title></head> <body link=#0000cc> <div id=wrapper> <div id=head> <div class=head_wrapper> <div class=s_form> <div class=s_form_wrapper> <div id=lg> <img hidefocus=true src=//www.baidu.com/img/bd_logo1.png width=270 height=129> </div> <form id=form name=f action=//www.baidu.com/s class=fm> <input type=hidden name=bdorz_come value=1> <input type=hidden name=ie value=utf-8> <input type=hidden name=f value=8> <input type=hidden name=rsv_bp value=1> <input type=hidden name=rsv_idx value=1> <input type=hidden name=tn value=baidu><span class="bg s_ipt_wr"><input id=kw name=wd class=s_ipt value maxlength=255 autocomplete=off autofocus></span><span class="bg s_btn_wr"><input type=submit id=su value=百度一下 class="bg s_btn"></span> </form> </div> </div> <div id=u1> <a href=http://news.baidu.com name=tj_trnews class=mnav>新闻</a> <a href=http://www.hao123.com name=tj_trhao123 class=mnav>hao123</a> <a href=http://map.baidu.com name=tj_trmap class=mnav>地图</a> <a href=http://v.baidu.com name=tj_trvideo class=mnav>视频</a> <a href=http://tieba.baidu.com name=tj_trtieba class=mnav>贴吧</a> <noscript> <a href=http://www.baidu.com/bdorz/login.gif?login&amp;tpl=mn&amp;u=http%3A%2F%2Fwww.baidu.com%2f%3fbdorz_come%3d1 name=tj_login class=lb>登录</a> </noscript> <script>document.write('<a href="http://www.baidu.com/bdorz/login.gif?login&tpl=mn&u='+ encodeURIComponent(window.location.href+ (window.location.search === "" ? "?" : "&")+ "bdorz_come=1")+ '" name="tj_login" class="lb">登录</a>');</script> <a href=//www.baidu.com/more/ name=tj_briicon class=bri style="display: block;">更多产品</a> </div> </div> </div> <div id=ftCon> <div id=ftConw> <p id=lh> <a href=http://home.baidu.com>关于百度</a> <a href=http://ir.baidu.com>About Baidu</a> </p> <p id=cp>&copy;2017&nbsp;Baidu&nbsp;<a href=http://www.baidu.com/duty/>使用百度前必读</a>&nbsp; <a href=http://jianyi.baidu.com/ class=cp-feedback>意见反馈</a>&nbsp;京ICP证030173号&nbsp; <img src=//www.baidu.com/img/gs.gif> </p> </div> </div> </div> </body> </html>
```

从响应头部，我们可以得到很多信息。 例如，从 Set-Cookie 头部可以查看到 Cookie 信息， 从 Etag 、 Last-Modified 等头部可以查看到与缓存相关的信息。 HTTP 头部不在本文的讨论范围内，不再赘述。

## 自动跳转
百度还有另外一个域名 www.baidu.cn ， 对该域名的访问将跳转到 www.baidu.com ：

```bash
$ curl -i www.baidu.cn
HTTP/1.1 302 Found
Location: http://www.baidu.com/
Date: Wed, 20 Jun 2018 00:44:03 GMT
Content-Length: 44
Content-Type: text/html; charset=utf-8

<a href="http://www.baidu.com/">Found</a>.
```

curl 请求 www.baidu.cn 后，服务器返回一个 302 跳转。 跳转地址由 Location 头部指定，为 http://www.baidu.com/ 。

实际上，我们可以让 curl 自动跳转，只需加上 -L 选项：

```bash
$ curl -iL www.baidu.cn
HTTP/1.1 302 Found
Location: http://www.baidu.com/
Date: Wed, 20 Jun 2018 00:44:30 GMT
Content-Length: 44
Content-Type: text/html; charset=utf-8

HTTP/1.1 200 OK
Accept-Ranges: bytes
Cache-Control: private, no-cache, no-store, proxy-revalidate, no-transform
Connection: Keep-Alive
Content-Length: 2381
Content-Type: text/html
Date: Wed, 20 Jun 2018 00:44:30 GMT
Etag: "588604dd-94d"
Last-Modified: Mon, 23 Jan 2017 13:27:57 GMT
Pragma: no-cache
Server: bfe/1.0.8.18
Set-Cookie: BDORZ=27315; max-age=86400; domain=.baidu.com; path=/

<!DOCTYPE html>
<!--STATUS OK--><html> <head><meta http-equiv=content-type content=text/html;charset=utf-8><meta http-equiv=X-UA-Compatible content=IE=Edge><meta content=always name=referrer><link rel=stylesheet type=text/css href=http://s1.bdstatic.com/r/www/cache/bdorz/baidu.min.css><title>百度一下，你就知道</title></head> <body link=#0000cc> <div id=wrapper> <div id=head> <div class=head_wrapper> <div class=s_form> <div class=s_form_wrapper> <div id=lg> <img hidefocus=true src=//www.baidu.com/img/bd_logo1.png width=270 height=129> </div> <form id=form name=f action=//www.baidu.com/s class=fm> <input type=hidden name=bdorz_come value=1> <input type=hidden name=ie value=utf-8> <input type=hidden name=f value=8> <input type=hidden name=rsv_bp value=1> <input type=hidden name=rsv_idx value=1> <input type=hidden name=tn value=baidu><span class="bg s_ipt_wr"><input id=kw name=wd class=s_ipt value maxlength=255 autocomplete=off autofocus></span><span class="bg s_btn_wr"><input type=submit id=su value=百度一下 class="bg s_btn"></span> </form> </div> </div> <div id=u1> <a href=http://news.baidu.com name=tj_trnews class=mnav>新闻</a> <a href=http://www.hao123.com name=tj_trhao123 class=mnav>hao123</a> <a href=http://map.baidu.com name=tj_trmap class=mnav>地图</a> <a href=http://v.baidu.com name=tj_trvideo class=mnav>视频</a> <a href=http://tieba.baidu.com name=tj_trtieba class=mnav>贴吧</a> <noscript> <a href=http://www.baidu.com/bdorz/login.gif?login&amp;tpl=mn&amp;u=http%3A%2F%2Fwww.baidu.com%2f%3fbdorz_come%3d1 name=tj_login class=lb>登录</a> </noscript> <script>document.write('<a href="http://www.baidu.com/bdorz/login.gif?login&tpl=mn&u='+ encodeURIComponent(window.location.href+ (window.location.search === "" ? "?" : "&")+ "bdorz_come=1")+ '" name="tj_login" class="lb">登录</a>');</script> <a href=//www.baidu.com/more/ name=tj_briicon class=bri style="display: block;">更多产品</a> </div> </div> </div> <div id=ftCon> <div id=ftConw> <p id=lh> <a href=http://home.baidu.com>关于百度</a> <a href=http://ir.baidu.com>About Baidu</a> </p> <p id=cp>&copy;2017&nbsp;Baidu&nbsp;<a href=http://www.baidu.com/duty/>使用百度前必读</a>&nbsp; <a href=http://jianyi.baidu.com/ class=cp-feedback>意见反馈</a>&nbsp;京ICP证030173号&nbsp; <img src=//www.baidu.com/img/gs.gif> </p> </div> </div> </div> </body> </html>
```

这时， curl 紧接着对 http://www.baidu.com/ 发起请求，得到百度首页的源码。

## Cookie

浏览器发起请求时自动将 Cookie 带上，而 curl 则需要通过 --cookie 选项显式指定：

```bash
$ curl --cookie 'BDORZ=27315' www.baidu.com
```

--cookie 可以写多次，以发送多个 Cookie 。

## 发送头部

如果需要给请求加上额外头部，可以通过 --header 选项指定：

```bash
$ curl --header 'Cookie: BDORZ=27315' www.baidu.com
```

这是另外一种发送 Cookie 的方式—— Cookie 本质上就是通过头部发送给服务端的。

同样， --header 也可以写多次以发送多个头部。

## 请求方法
HTTP 请求方法有多种， curl 通过 -X 选项可以指定。

GET 是 curl 的默认请求方法，上文对百度首页的请求等价于：

```bash
$ curl -X GET www.baidu.com
```

其他方法以此类推：
```bash
# 发送POST请求
$ curl -X POST www.example.com/some/path

# 发送DELETE请求
$ curl -X DELETE www.example.com/some/path
```

## 发送数据

在 HTTP 中，我们可以通过 请求体 向服务端发送数据。 一般还需要与 Content-Type 头部配合，明确将数据类型告知服务端：

```bash
$ curl -X POST \
    -d '{"name": "foo", "value": 123}' \
    --header 'Content-Type: application/json' \
    www.example.com/some/path
```

这个例子，我们通过 -d 选项向服务端发送了一个 json 数据。 注意到，我们发送了一个 Content-Type 头部，明确告诉服务端被发送的数据是 json 类型。

### 从子命令读取数据

如果数据是由其他命令生成的，可以在 -d 选项后写 shell 子命令：

```bash
$ curl -X POST \
    -d `echo '{"name": "foo", "value": 123}'` \
    --header 'Content-Type: application/json' \
    www.example.com/some/path
```


反引号 `` 中间是一个子命令， shell 先执行该命令，再将它的输出作为 curl 的命令行参数。因此，子命令输出将作为 -d 选项的数据，被 POST 到服务器。

### 从文件读取数据

如果要发送的数据比较多，直接在命令行中输入肯定不方便。好在我们可以将数据保存在文件中，再通过 --data-binary 指定：


```bash
$ curl -X POST \
    --data-binary /path/to/data.json \
    --header 'Content-Type: application/json' \
    www.example.com/some/path
```


### 从标准输入读取数据

如果待发送数据由其他命令生成，并通过管道重定向给 curl 命令，它可以直接从标准输入中读取：

```bash
echo '{"name": "foo", "value": 123}' | curl -X POST \
    --data-binary @- \
    --header 'Content-Type: application/json' \
    www.example.com/some/path
```

> --data-binary 选项表示从文件中读取待发送数据，特殊符号 @- 则表示从标准输入中读取。

