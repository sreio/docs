?> NGINX 相关内容

## 1\. nginx配置文件

```nginx
#==========================nginx全局配置==========================#
# 指定 nginx 进程运行的用户，这里是 nginx，默认为nobody。
user nginx;  

# 指定 nginx 使用多少个 worker 进程处理请求，这里使用了 auto，表示根据 CPU 核心数自动分配。
worker_processes auto;  

# 指定 nginx 主进程的 PID 文件路径。
pid /run/nginx.pid;  

#指定错误日志文件的路径
error_log /var/log/nginx/error.log;  
#==========================nginx全局配置==========================#

##==========================events==========================##
# 事件模块的配置
events {
    # 默认值512,设置每个 worker 进程的最大连接数。
    worker_connections  1024;    

    # 默认值off,控制是否在一次事件循环中accept多个连接请求。启用 multi_accept 可以减少 CPU 的使用和系统调用的次数，也可能会导致每个请求的响应时间增加
    multi_accept on; 

    # 使用连接互斥锁进行顺序的accept()系统调用，防止惊群现象发生，默认为on
    accept_mutex on;   

    # 如果一个进程没有互斥锁，它将延迟至少多长时间。默认情况下，延迟是500ms 
    accept_mutex_delay 500ms

    # 指定事件驱动模块,默认值select,这个驱动模块在大量连接时，性能较差，这里使用了 epoll。 可选select|poll|kqueue|epoll|resig|/dev/poll|eventport
    use epoll;      

    # 设置 accept 互斥锁等待时间。
    accept_mutex_delay 500ms; 

    # 实验模块，仅在 FreeBSD 下可用,aio 启用则使用异步I/O,不启用则使用同步I/O,默认不启用
    aio threads; # 指定异步 I/O 模块，这里使用了 threads。
    aio_write on; # 启用异步写入操作。
    aio_read on; # 启用异步读取操作。

    # 事件驱动模块 use epoll时配置
    epoll_events 1024; # 默认值为 512，即每次事件循环最多处理 512 个事件。
    epoll_event_connections 512; # 默认值为 2048，即每个事件最多处理 2048 个连接。
    epoll_timeout 1s; # 默认值为 0，即 epoll 模块不会超时，等待事件的时间取决于操作系统。

    //事件驱动模块 use kqueue时配置,仅在 FreeBSD 下可用
    kqueue_events 1024;
    kqueue_event_connections 512;
    kqueue_timeout 1s;
}
##==========================events==========================##

##==========================http==========================##
# 主要用于处理 HTTP 请求和响应，包括路由、反向代理、缓存、日志等功能。
http {
    # 导入 MIME 类型的配置文件。
    include  /etc/nginx/mime.types;   

    # 定义默认的 MIME 类型，默认为text/plain 
    default_type  application/octet-stream; 

    # 定义访问日志格式 main
    log_format main '$remote_addr–$remote_user [$time_local] $request $status $body_bytes_sent $http_referer $http_user_agent $http_x_forwarded_for'; 

    # 指定访问日志文件的路径和格式。
    access_log  /var/log/nginx/access.log main; 

    # 指定错误日志文件的路径和格式。
    error_log  /var/log/nginx/error.log main;   

    # 默认开启,直接把文件内容从磁盘读入内核缓冲区,尤其是在处理大文件时，效果更为明显。
    sendfile on;  

    # 每个进程每次调用传输数量不能大于设定的值，默认为0，即不设上限。
    sendfile_max_chunk 100k;  

    # 客户端与服务器之间的连接超时时间，默认值为 75s。
    keepalive_timeout 75;  

    # 在一个持久连接上最多允许的请求数量，默认值为 100。
    keepalive_requests 100; 

    # 是否开启 Gzip 压缩，默认值为 off。
    gzip on; 
    gzip_types text/plain text/css application/json application/javascript application/xml; # 压缩文件类型列表
    gzip_min_length 20; # 只压缩大于该长度的文件，单位为字节，默认值为 20。
    gzip_buffers 4 8k; # 第一个参数表示缓冲区的数量，第二个参数表示每个缓冲区的大小。 默认值为 4 8k。

    # 当服务器返回错误码时，可以显示指定的错误页面。
    error_page error_page 404 /404.html;  

    ###==========================upstream=========================###
    # upstream 模块用于配置反向代理服务器组，可以将客户端请求转发到多台服务器进行处理，从而实现负载均衡和高可用性。
    upstream backend  {   
      # 简单的循环负载均衡
      server backend1.example.com;
      server backend2.example.com;

      # 使用权重进行负载平衡
      server backend3.example.com weight=3; #指定服务器的权重，缺省为 1，可以是任意正整数。
      server backend4.example.com weight=2;

      # 使用IP Hash值进行权重进行负载平衡,确保来自同一 IP 的请求总是发送到同一服务器。
      ip_hash;

      # 用最少的连接进行负载均衡,动态地将请求发送到最空闲的服务器。
      least_conn;

      # 使用服务器响应时间进行负载平衡,将请求发送到响应时间最短的服务器。
      fair;

      # 根据请求 URL 的哈希值进行负载平衡
      hash $remote_addr consistent;

      # 随机发送到服务器
      random;

      # 为backend分配一个共享内存区域。
      zone backend_zone 64k;

      # 指定与后端服务器的 TCP 连接复用数，缺省为 1。
      keepalive 16; 

      # 指定与后端服务器的 TCP 连接最大请求数，超过此数量，连接将关闭并重新建立新连接。默认为 100。
      keepalive_requests 100;

      # 指定与后端服务器的 TCP 连接空闲超时时间，默认为 75 秒。
      keepalive_timeout 60s;
    }
    ###==========================upstream=========================###

    ###==========================server=========================###
    server {
      # 监听端口
      listen 80;

      # 指定服务器的主机名。它可以是域名、IP 地址或*匹配任何主机名,也可以配置多个域名
      server_name example.com;

      # 根目录
      root /var/www/example.com;

      # 限速 每秒4K
      set $limit_rate 4k;

      # 当用户访问example.com 或 example.com/ 时
      location / {

      # 指定用于提供文件的回退机制。如果请求的文件不存在，nginx将尝试返回fallback
        try_files index.html index.htm @fallback;
      }
      # 上面try files 回退到这里
      location @fallback {
         root  /var/www/error;
         index index.html;
      }



      # 当用户访问example.com/api时
      location /api {

        # 反向代理到 http://backend/api; 本配置中 backend 是个负载均衡器
        proxy_pass http://backend/api;
      }

      # 当用户访问example.com/images时
      location /images/ {
        # alias 看起来类似于 root 指令，但文档根目录没有改变，只是用于当前请求的路径。 
        # /images/top.gif 将返回 /var/www/images/top.gif
        alias /var/www/images/;

        # 设置缓存时间为1天,原理是Header 设置Cache-Control
        expires 1d;

        # 添加header
        add_header Cache-Control "public";
      }

      location ~ \.php$ {
        include fastcgi_params;  
        fastcgi_index index.php;  
        # 使用fastcgi协议 转发到 phpfpm的unix socket文件 phpfpm 和 nginx 同一台机器才可以这么配置
        fastcgi_pass unix:/var/run/php/php7.x-fpm.sock;
        # 使用fastcgi协议 转发到某个服务器某个端口
        fastcgi_pass 127.0.0.1:9000;  
        # 使用fastcgi协议 转发到负载均衡器
        fastcgi_pass http://backend/

        deny 127.0.0.1;  #拒绝的ip
        allow 172.18.5.54; #允许的ip           
      }

      # SSL 配置 监听443
      listen 443 ssl;

      # 证书路径
      ssl_certificate /etc/ssl/certs/example.com.crt;

      # 证书私钥
      ssl_certificate_key /etc/ssl/private/example.com.key;

      # HTTP/2 配置 监听443
      listen 443 ssl http2;

      # 设置安全header
      add_header X-Content-Type-Options "nosniff" always;
      add_header X-Frame-Options "SAMEORIGIN" always;
      add_header X-XSS-Protection "1; mode=block" always;
    }

    ###==========================server=========================###
}
##==========================http==========================##
```


## **Linux相关知识点**
>Tips：文章内容会随着更新，变得越来越多，先挑对自己有用的看
- [Nginx实现一个IP访问总流量限制](#1)
- [统计服务器所有url被请求的数量](#2)
- [查找请求数前20个IP](#3)
- [统计文件中某个字符串出现的次数](#4)
- [监听文件](#5)
- [查看文件大小](#6)
- [查看ip来源地](#7)
- [查看磁盘使用情况](#8)
- [创建软链接](#9)
- [查看是否监听xx端口](#10)
- [Nginx之解决跨域问题](#11)
- [Nginx之配置ssl](#12)
- [Nginx之开放防火墙端口](#13)
- [Linux之 cat 命令](#14)
- [Nginx之实现网络负载均衡](#15)
- [Linux之locate](#16)

<a name="1"></a>
### **1.Nginx实现一个IP访问总流量限制**

```nginx
http {
    limit_conn_zone $binary_remote_addr zone=conn_zone:10m;
    limit_req_zone $binary_remote_addr zone=req_zone:10m rate=1r/s;
    # 其他配置
}
server {
    listen 80;
    server_name example.com;

    # 限制单个客户端 IP 最大连接数为 2
    limit_conn conn_zone 2;
    # 限制单个客户端 IP 1s 内最多发起 1 个请求
    limit_req zone=req_zone burst=5;
    # 计算客户端 IP 累计请求流量，限制累计请求流量为 1GB
    set $limit_rate 128k;
    limit_rate_after 500M;
    limit_rate 1m;
    # 其他处理逻辑
}
```
<a name="2"></a>
## **2.统计服务器所有url被请求的数量**

```bash
#统计其他端口时：netstat -pnt | grep :xx | wc -l
netstat -pnt | grep :443 | wc -l
```
<a name="3"></a>
## **3.查找请求数前20个IP**

```bash
#服务器被恶意高频访问时可以用到
netstat -ant |awk '/:443/{split($5,ip,":");++A[ip[1]]}END{for(i in A) print A[i],i}' |sort -rn|head -n20
```
<a name="4"></a>
## **4.统计文件中某个字符串出现的次数**

```bash
#如统计文件api_error.log中error关键词出现的次数
grep -o 'error' /www/payment/runtime/logs/api_error.log | wc -l
```
<a name="5"></a>
## **5.监听文件**

```bash
#可以监听整个文件，也可根据关键词监听
cd /www/payment/runtime/logs/

#-f 常用于查阅正在改变的日志文件
tail -f xxx.log #监听整个文件
tail -f xxx.log | grep 'xxx' #根据关键词监听
tail -Nf xxx.log  #N为要查看的、最近多少条日志，例如tail -100f xxx.log 显示 xxx.log 里尾部100行的内容，且不断刷新。
```
<a name="6"></a>
## **6.查看文件大小**
```bash
#以下是本人开发中常用到的
一、du命令
du命令是查看使用空间的，但是与df命令不同的是Linux du命令是对文件和目录磁盘使用的空间的查看，还是和df命令有一些区别的。

du -h filepath 直接得出人好识别的文件大小

du -h ljl.txt

或者
du -ah 当前目录下所有文件的大小

二、ls命令
ls命令用来显示目标列表，在Linux中是使用率较高的命令。ls命令的输出信息可以进行彩色加亮显示，以分区不同类型的文件。

ls -l filepath 第五列为文件字节数

ls -l ljl.txt

ls -h filepath h表示human, 加-h参数得到人好读的文件大小

ls -lh ljl.txt
```
<a name="7"></a>
## **7.查看ip来源地**

```bash
#使用这个是为了查看异常登录地址
yum -y install whois
whois 119.53.224.203
```
<a name="8"></a>
## **8. 查看磁盘使用情况**

```bash
df -h
```
<a name="9"></a>
## **9.创建软链接**

```bash
ln -s [源文件或目录] [目标文件或目录]

例如：创建软链时，先进入根目录的bin目录下

cd /bin

从/bin这个路径创建软链nginx 引入/usr/local/nginx/sbin/nginx

ln -s /usr/local/nginx/sbin/nginx nginx

创建软链之后 重载nginx 
就可以从 /usr/local/nginx/sbin/nginx -s reload 简化为 nginx -s reload
```
<a name="10"></a>
## **10.查看是否监听9000端口（其他端口雷同）**

```bash
netstat -ant | grep 9000
```
<a name="11"></a>
## **11.Nginx之 解决跨域问题**

```nginx
#server模块中添加以下内容，修改完后，记得重启nginx
add_header  Access-Control-Allow-Headers '*';
add_header  Access-Control-Allow-Origin '*';
add_header  Access-Control-Allow-Credentials 'true';
add_header  Access-Control-Allow-Methods 'GET,POST,OPTIONS';

#跨域参数解释
#Access-Control-Allow-Origin：服务器默认是不被允许跨域的。给Nginx服务器配置Access-Control-Allow-Origin *后，表示服务器可以接受所有的请求源(Origin),即接受所有跨域的请求。
#Access-Control-Allow-Methods：允许通过的方法，可以防止Content-Type is not allowed by Access-Control-Allow-Headers in preflight response的错误信息
#Access-Control-Allow-Credentials true 表示允许跨域请求携带 cookie
#Access-Control-Allow-Headers：为了防止出现Request header field Content-Type is not allowed by Access-Control-Allow-Headers in preflight response的错误信息。这个错误表示当前请求Content-Type的值不被支持。其实是我们发起了”application/json”的类型请求导致的
```
<a name="12"></a>
## **12.Nginx之 配置ssl(小白看过来)**
```nginx
server {
    listen 443 ssl;#监听443端口
	#server_name 购买域名后解析到该服务器上，如果没有域名，也可以给此项目单独开端口
    server_name  api.applet.xxx.com;
    root        /xxx/xxxx/xxxx;#root 项目地址
    index       index.php index.html index.htm;#项目入口文件

	#存放证书的地址.pem和.key文件
    ssl_certificate /usr/local/nginx/conf/vhost/cert/xxx.pem;
    ssl_certificate_key /usr/local/nginx/conf/vhost/cert/xxx.key;
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;

    if (!-e $request_filename){
        rewrite ^/(.*) /index.php last;
    }

    location ~ \.php$ {
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
            include        fastcgi_params;
    }
    error_log  /var/log/nginx/api_applet.log error;
}

```

<a name="13"></a>
## **13.Nginx之 开放防火墙端口**

> 紧接着上面的 12.Nginx配置ssl 中 server_name 提到的给项目开端口

```bash
#这里讲到的是CentOS7,CentOS7的防火墙和CentOS6不一样了， CentOS 6 系列中的 iptables 相关命令不能用了，Centos7中使用firewalld代替了原来的iptables。

#停止firewall
systemctl stop firewalld.service

#禁止firewall开机启动
systemctl disable firewalld.service

#添加8022端口
firewall-cmd --zone=public --add-port=8022/tcp --permanent

#多个端口:
firewall-cmd --zone=public --add-port=80-90/tcp --permanent

#命令含义：
--zone #作用域
--add-port=8022/tcp #添加端口，格式为：端口/通讯协议
--permanent #永久生效，没有此参数重启后失效

#centos7查看防火墙所有信息
firewall-cmd --list-all

#centos7查看防火墙开放的端口信息
firewall-cmd --list-ports

#删除8022端口
firewall-cmd --zone=public --remove-port=8022/tcp --permanent

#重启防火墙
firewall-cmd --reload

常用命令介绍：
firewall-cmd --state                ##查看防火墙状态，是否是running
firewall-cmd --reload               ##重新载入配置，比如添加规则之后，需要执行此命令
firewall-cmd --get-zones            ##列出支持的zone
firewall-cmd --get-services         ##列出支持的服务，在列表中的服务是放行的
firewall-cmd --query-service ftp    ##查看ftp服务是否支持，返回yes或者no
firewall-cmd --add-service=ftp      ##临时开放ftp服务
firewall-cmd --add-service=ftp --permanent     ##永久开放ftp服务
firewall-cmd --remove-service=ftp --permanent  ##永久移除ftp服务
firewall-cmd --add-port=80/tcp --permanent     ##永久添加80端口
```

<a name="14"></a>
## **14.Linux之 cat 命令**

```bash
#是不是以为cat只有 cat filename 这个命令？

#不不不，它还可以更骚气。接下来介绍一下cat命令常用又不常用的命令

1. 显示文件内容

使用cat命令可以打印出文件的内容，如：

cat filename

这将把文件的所有行打印到终端上。

2. 将多个文件合并

使用cat命令还可以将多个文件合并成一个文件，如：

cat file1 file2 > file3

这将把file1和file2的内容合并成一个新文件file3。

3. 使用管道输出内容

在Linux环境下，使用管道可以将一个命令的输出作为另一个命令的输入，如：

cat filename | grep keyword

这将把filename文件中包含keyword的行输出。

4. 压缩文件

cat命令还可以用于压缩文件。将cat输出重定向到一个tar命令中，可以将文件压缩成一个tar文件，如：

cat file1 file2 | tar -czf archive.tar.gz -

这将把file1和file2打包成一个tar文件，然后使用gzip进行压缩。

5. 重定向输出

使用重定向符号可以将cat命令的输出重定向到文件中，如：

cat filename > newfile

这将把filename的内容输出到newfile中，如果newfile不存在则会创建一个新的文件。

6.反向输出

#打印内容
[root@VM-4-4-centos data]# cat 1.txt
我是第一行
我是第二行
我是第三行

#反向打印
[root@VM-4-4-centos data]# tac 1.txt
我是第三行
我是第二行
我是第一行
```

<a name="15"></a>
## **15. Nginx之 实现网络负载均衡**

```nginx
nginx内置负载均衡策略主要分为三大类，分别是轮询、最少连接和ip hash

1. 轮询

以循环方式分发对应用服务器的请求，将请求平均分发到每台服务器上。

1.1 普通轮询方式
该方式是默认方式，轮询适合服务器配置相当，无状态且短平快的服务使用。另外在轮询中，如果服务器挂掉，会自动剔除该服务器。

http {
    # 定义转发分配规则
    upstream myapp1 {
        server baidu.com; # 要转发到的服务器，如ip、ip:端口号、域名、域名:端口号
        server baidu2.com:8088;
        server 192.168.0.1:8088;
    }
    server {
        listen 443; # nginx监听的端口
        location / {
            # 使用myapp1分配规则，即刚自定义添加的upstream节点
            # 将所有请求转发到myapp1服务器组中配置的某一台服务器上
            proxy_pass http://myapp1;
        }
    }
}

1.2权重轮询方式
如果在 upstream 中配置的server参数后追加 weight 配置，则会根据配置的权重进行请求分发。此策略可以与least_conn和ip_hash结合使用，适合服务器的硬件配置差别比较大的情况。

# 定义转发分配规则
upstream myapp1 {
	server baidu1.com weight=1; # 该台服务器接受1/6的请求量
	server baidu2.com:8088 weight=2; # 该台服务器接受2/6的请求量
	server 198.16.0.1:8088 weight=3; # 该台服务器接受3/6的请求量;
}

2. 最少连接

轮询算法是把请求平均的转发给各个后端，使它们的负载大致相同；但是，有些请求占用的时间很长，会导致其所在的后端负载较高。这种情况下，least_conn这种方式就可以达到更好的负载均衡效果，适合请求处理时间长短不一造成服务器过载的情况

# 定义转发分配规则
upstream myapp1 {
	least_conn; # 把请求分派给连接数最少的服务器
	server baidu.com;
	server baidu1.com:8088;
	server 192.168.0.100:8088;
}

2.3 ip hash

这个方法确保了相同的客户端的请求一直发送到相同的服务器，这样每个访客都固定访问一个后端服务器。如用户需要分片上传文件到服务器下，然后再由服务器将分片合并，这时如果用户的请求到达了不同的服务器，那么分片将存储于不同的服务器目录中，导致无法将分片合并，该场景则需要使用ip hash策略。

需要注意的是，ip_hash不能与backup同时使用，另外当有服务器需要剔除，必须手动down掉，此模式适合有状态服务，比如session。

# 定义转发分配规则
upstream myapp1 {
	ip_hash; # #保证每个请求固定访问一个后端服务器
	server srv1.com;
	server srv2.com:8088;
	server 192.168.0.100:8088;
}
```

<a name="16"></a>
## **16. Linux之 locate**

```bash
#命令简介
locate(locate) 命令用来查找文件或目录。 要比find -name快得多，原因在于它不搜索具体目录，而是搜索一个数据库/var/lib/mlocate/mlocate.db 。这个数据库中含有本地所有文件信息。Linux系统自动创建这个数据库, 并且每天自动更新一次，因此，我们在用whereis和locate 查找文件时，有时会找到已经被删除的数据，或者刚刚建立文件，却无法查找到，原因就是因为数据库文件没有被更新。为了避免这种情况，可以在使用locate之前，先使用updatedb命令，手动更新数据库。整个locate工作其实是由四部分组成的:

1.  /usr/bin/updatedb            主要用来更新数据库，通过crontab自动完成的
2.  /usr/bin/locate              查询文件位置
3.  /etc/updatedb.conf           updatedb的配置文件
4.  /var/lib/mlocate/mlocate.db  存放文件信息的文件

#用法
#一般情况我们只需要输入 locate your_file_name 即可查找指定文件。
locate [OPTION]... [PATTERN]...

-b,  --basename -- 仅匹配路径名的基本名称
-c,  --count -- 只输出找到的数量
-d,  --database DBPATH -- 使用 DBPATH 指定的数据库，而不是默认数据库 /var/lib/mlocate/mlocate.db
-e,  --existing -- 仅打印当前现有文件的条目
-1   -- 如果 是 1．则启动安全模式。在安全模式下，使用者不会看到权限无法看到 的档案。这会始速度减慢，因为 locate 必须至实际的档案系统中取得档案的 权限资料。
-0,  --null -- 在输出上带有NUL的单独条目
-S,  --statistics -- 不搜索条目，打印有关每个数据库的统计信息
-q   -- 安静模式，不会显示任何错误讯息。
-P,  --nofollow, -H -- 检查文件存在时不要遵循尾随的符号链接
-l,  --limit, -n LIMIT -- 将输出（或计数）限制为LIMIT个条目
-n   -- 至多显示 n个输出。
-m,  --mmap -- 被忽略，为了向后兼容
-r,  --regexp REGEXP -- 使用基本正则表达式
     --regex -- 使用扩展正则表达式
-q,  --quiet -- 安静模式，不会显示任何错误讯息
-s,  --stdio -- 被忽略，为了向后兼容
-o,  -- 指定资料库存的名称。
-h,  --help -- 显示帮助
-i,  --ignore-case -- 忽略大小写
-V,  --version -- 显示版本信息

# 实例
#查找 php.ini 文件，输入以下命令：
locate php.ini

搜索 etc 目录下所有以 sh 开头的文件 ：
locate /etc/sh

忽略大小写搜索当前用户目录下所有以 r 开头的文件 ：
locate -i ~/r

# 附加说明
1.locate 与 find 不同: find 是去硬盘找，locate只在资料库中找。
2.locate 的速度比 find 快，它并不是真的查找，而是查数据库，一般文件数据库在 /var/lib/mlocate/mlocate.db 中，所以 locate 的查找并不是实时的，而是以数据库的更新为准，一般是系统自己维护，也可以手工升级数据库 ，命令为： updatedb
```
