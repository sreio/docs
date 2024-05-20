## Nginx 面试题

### Nginx 是如何实现高并发的？

如果一个 server 采用一个进程(或者线程)负责一个 request 的方式，那么进 程数就是并发数。那么显而易⻅的，
就是会有很多进程在等待中。等什么？最 多的应该是等待网络传输。其缺点胖友应该也感觉到了，此处不述。

而 Nginx 的异步非阻塞工作方式正是利用了这点等待的时间。在需要等待的 时候，这些进程就空闲出来待命了。
因此表现为少数几个进程就解决了大量的 并发问题。

Nginx 是如何利用的呢，简单来说：同样的 4 个进程，如果采用一个进程负 责一个 request 的方式，那么，同
时进来 4 个 request 之后，每个进程就 负责其中一个，直至会话关闭。期间，如果有第 5 个 request 进来了。
就无 法及时反应了，因为 4 个进程都没干完活呢，因此，一般有个调度进程，每当 新进来了一个 request ，就
新开个进程来处理。

Nginx 不这样，每进来一个 request ，会有一个 worker 进程去处理。但不 是全程的处理，处理到什么程度
呢？处理到可能发生阻塞的地方，比如向上游 （后端）服务器转发 request ，并等待请求返回。那么，这个处理
的 worker 不会这么傻等着，他会在发送完请求后，注册一个事件：“如果 upstream 返 回了，告诉我一声，我再
接着干”。于是他就休息去了。此时，如果再有 request 进来，他就可以很快再按这种方式处理。而一旦上游服务
器返回了， 就会触发这个事件，worker 才会来接手，这个 request 才会接着往下走。 **这就是为什么说，Nginx
基于事件模型。**

由于 web server 的工作性质决定了每个 request 的大部份生命都是在网络 传输中，实际上花费在 server 机器
上的时间片不多。这是几个进程就解决高 并发的秘密所在。即：

**webserver 刚好属于网络 IO 密集型应用，不算是计算密集型。异步，非阻 塞，使用 epol- ，和大量细节处的
优化，也正是 Nginx 之所以然的技术基 石。**


### 请解释 Nginx 如何处理 HTTP 请求。

Nginx 使用反应器模式。主事件循环等待操作系统发出准备事件的信号，这样 数据就可以从套接字读取，在该实
例中读取到缓冲区并进行处理。单个线程可 以提供数万个并发连接。

### 为什么要做动、静分离？

在我们的软件开发中，有些请求是需要后台处理的（如：.jsp,.do 等等），有些 请求是不需要经过后台处理的
（如：css、html、jpg、js 等等），这些不需要 经过后台处理的文件称为静态文件，否则动态文件。因此我们后台
处理忽略静 态文件，但是如果直接忽略静态文件的话，后台的请求次数就明显增多了。在 我们对资源的响应速度
有要求的时候，应该使用这种动静分离的策略去解决

动、静分离将网站静态资源（HTML，JavaScript，CSS 等）与后台应用分开 部署，提高用户访问静态代码的速
度，降低对后台应用访问。这里将静态资源 放到 nginx 中，动态资源转发到 tomcat 服务器中,毕竟 Tomcat 的优
势是处理 动态请求。

### nginx 是如何实现高并发的？

一个主进程，多个工作进程，每个工作进程可以处理多个请求，每进来一个 request，会有一个 worker 进程去处
理。但不是全程的处理，处理到可能发生 阻塞的地方，比如向上游（后端）服务器转发 request，并等待请求返
回。那 么，这个处理的 worker 继续处理其他请求，而一旦上游服务器返回了，就会 触发这个事件，worker 才会
来接手，这个 request 才会接着往下走。由于 web server 的工作性质决定了每个 request 的大部份生命都是在网
络传输中， 实际上花费在 server 机器上的时间片不多。这是几个进程就解决高并发的秘密 所在。即 webserver
刚好属于网络 IO 密集型应用，不算是计算密集型。

### Nginx 静态资源?

静态资源访问，就是存放在 nginx 的 html ⻚面，我们可以自己编写。

### Nginx 配置高可用性怎么配置？

#### 当上游服务器(真实访问服务器)，一旦出现故障或者是没有及时相应的话，应

#### 该直接轮训到下一台服务器，保证服务器的高可用。

Nginx 配置代码：

```
server {
```
```
listen 80;
```
```
server\_name http://www.lijie.com;cc nginx 发送给上游服务器(真实访问的服 务器)超时时间
```
```
proxy\_send\_timeout 1s;###
```
```
nginx 接受上游服务器(真实访问的服务器)超时时间
```
```
proxy\_read\_timeout 1s;
```
```
index index.html index.htm;
```

### 502 错误可能原因

```
FastCGI 进程是否已经启动
FastCGI worker 进程数是否不够
FastCGI 执行时间过⻓
fastcgi_connect_timeout 300; - fastcgi_send_timeout 300;
fastcgi_read_timeout 300;
FastCGI Buffer 不够
nginx 和 apache 一样，有前端缓冲限制，可以调整缓冲参数 - fastcgi_buffer_size 32k;
fastcgi_buffers 8 32k;
Proxy Buffer 不够
如果你用了 Proxying，调整
proxy_buffer_size 16k;
proxy_buffers 4 16k;
php 脚本执行时间过⻓
将 php-fpm.conf 的 0s 的 0s 改成一个时间
```
### 在 Nginx 中，解释如何在 URL 中保留双斜线?

要在 URL 中保留双斜线，就必须使用 语法:merge_slashes [on/off] 默认值: merge_slashes on

环境: http，server

merge_slashes_off;

### Nginx 服务器上的 Master 和 Worker 进程分别是什么?

Master 进程：读取及评估配置和维持 ；Worker 进程：处理请求。

### Nginx 的优缺点？

#### 优点：

#### 占内存小，可实现高并发连接，处理响应快。

```
可实现 HTTP 服务器、虚拟主机、方向代理、负载均衡。 - Nginx 配置简单。
可以不暴露正式的服务器 IP 地址。
```
缺点：

动态处理差，nginx 处理静态文件好,耗费内存少，但是处理动态⻚面则很鸡 肋，现在一般前端用 nginx 作为反向
代理抗住压力。

## RabbitMQ

#### }

#### }


## RabbitMQ

### RabbitMQ routing 路由模式

消息生产者将消息发送给交换机按照路由判断,路由是字符串(info) 当前产生的消息携 带路由字符(对象的方法)，交
换机根据路由的 key，只能匹配上路由 key 对应的消息队列, 对应的消费者才能消费消息。

根据业务功能定义路由字符串。

从系统的代码逻辑中获取对应的功能字符串,将消息任务扔到对应的队列中。

业务场景：error 通知、EXCEPTION、错误通知的功能、传统意义的错误通知、客户 通知、利用 key 路由，可以
将程序中的错误封装成消息传入到消息队列中，开发者可以自 定义消费者，实时接收错误。

### 消息怎么路由？

消息提供方->路由->一至多个队列消息发布到交换器时，消息将拥有一个路由键 （routing key），在消息创建时设
定。通过队列路由键，可以把队列绑定到交换器上。消 息到达交换器后，RabbitMQ 会将消息的路由键与队列的
路由键进行匹配（针对不同的交

换器有不同的路由规则）。

常用的交换器主要分为一下三种：

```
fanout：如果交换器收到消息，将会广播到所有绑定的队列上。
direct：如果路由键完全匹配，消息就被投递到相应的队列。
topic：可以使来自不同源头的消息能够到达同一个队列。 使用 topic 交换器时，可
```
以使用通配符。

### RabbitMQ publish/subscribe 发布订阅(共享资源)


#### 每个消费者监听自己的队列。

```
生产者将消息发给 broker，由交换机将消息转发到绑定此交换机的每个队列，每个绑
```
定交换机的队列都将接收到消息。

### 能够在地理上分开的不同数据中心使用 RabbitMQ cluster 么？

#### 不能。

第一，你无法控制所创建的 queue 实际分布在 cluster 里的哪个 node 上（一般使用

HAProxy + cluster 模型时都是这样），这可能会导致各种跨地域访问时的常⻅问题。 第二，Erlang 的 OTP 通
信框架对延迟的容忍度有限，这可能会触发各种超时，导致业务 疲于处理。

第三，在广域网上的连接失效问题将导致经典的“脑裂”问题，而 RabbitMQ 目前无法 处理（该问题主要是说
Mnesia）。

### RabbitMQ 有那些基本概念？

```
Broker：简单来说就是消息队列服务器实体。
Exchange：消息交换机，它指定消息按什么规则，路由到哪个队列。
Queue：消息队列载体，每个消息都会被投入到一个或多个队列。
Binding：绑定，它的作用就是把 exchange 和 queue 按照路由规则绑定起来。
Routing Key：路由关键字，exchange 根据这个关键字进行消息投递。
VHost：vhost 可以理解为虚拟 broker ，即 mini-RabbitMQ server。其内部均含
```
有独立的 queue、exchange 和 binding 等，但最最重要的是，其拥有独立的权限 系统，可以做到 vhost 范围
的用户控制。当然，从 RabbitMQ 的全局⻆度，vhost 可以作为不同权限隔离的手段（一个典型的例子就是不同
的应用可以跑在不同的 vhost 中）。

```
Producer：消息生产者，就是投递消息的程序。
Consumer：消息消费者，就是接受消息的程序。
Channel：消息通道，在客户端的每个连接里，可建立多个 channel，每个 channel
```
代表一个会话任务。

```
由 Exchange、Queue、RoutingKey 三个才能决定一个从 Exchange 到 Queue 的唯
```
一的线路。


### 什么情况下会出现 blackholed 问题？

blackholed 问题是指，向 exchange 投递了 message ，而由于各种原因导致该 message 丢失，但发送者却不
知道。可导致 blackholed 的情况：1.向未绑定 queue 的 exchange 发送 message；2.exchange 以
binding_key key_A 绑定了 queue queue_A，但向该 exchange 发送 message 使用的 routing_key 却是
key_B。

### 什么是消费者 Consumer?

#### 消费消息，也就是接收消息的一方。

消费者连接到 RabbitMQ 服务器，并订阅到队列上。消费消息时只消费消息体，丢弃标 签。

### 消息如何分发？

```
若该队列至少有一个消费者订阅，消息将以循环（round-robin）的方式发送给消费
```
者。每条消息只会分发给一个订阅的消费者（前提是消费者能够正常处理消息并进行 确认）。

```
通过路由可实现多消费的功能
```
### Basic.Reject 的用法是什么？

该信令可用于 consumer 对收到的 message 进行 reject 。若在该信令中设置 requeue=true，则当
RabbitMQ server 收到该拒绝信令后，会将该 message 重新发 送到下一个处于 consume 状态的 consumer
处（理论上仍可能将该消息发送给当前 consumer）。若设置 requeue=false ，则 RabbitMQ server 在收到拒
绝信令后，将直 接将该 message 从 queue 中移除。

另外一种移除 queue 中 message 的小技巧是，consumer 回复 Basic.Ack 但不对获 取到的 message 做任何
处理。而 Basic.Nack 是对 Basic.Reject 的扩展，以支持一次 拒绝多条 message 的能力。

### 什么是 Binding 绑定？

通过绑定将交换器和队列关联起来，一般会指定一个 BindingKey,这样 RabbitMq 就知道 如何正确路由消息到队列
了。