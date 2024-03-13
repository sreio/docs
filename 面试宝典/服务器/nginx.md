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