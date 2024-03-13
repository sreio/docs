?> php-fpm 配置文件解析


```nginx
; 该文件中的所有配置均以分号 (;) 开头，代表注释行或不使用的指令

; 全局配置
[global]
; 进程 ID 文件位置,默认none
pid = /var/run/php-fpm.pid

; 错误日志文件位置,默认#INSTALL_PREFIX#/log/php-fpm.log
error_log = /var/log/php-fpm.log

; 日志级别,枚举值:alert, error, warning, notice, debug,默认notice
log_level = notice

; 日志限制每行字符数,默认1024,PHP>=7.3.0
log_limit = 1024

; 是否启用日志缓冲,如果启用了 log_buffering，PHP-FPM 会将所有的日志消息缓存在内存中，直到缓冲区填满或达到了一定的时间限制，然后再将缓冲区中的所有消息一次性写入磁盘。默认yes,PHP>=7.3.0
log_buffering = yes

; 每 60 秒检查一次工作进程崩溃次数达到emergency_restart_threshold则重新启动主进程。默认值0
emergency_restart_interval = 60s/1m/2h/3d

; 默认值为 0，表示禁用自动重启。
emergency_restart_threshold = 0

; 指定 PHP-FPM 子进程可以响应来自主进程的控制信号的最长时间,如果worker进程10s没有相应则终止worker进程,默认为0s
process_control_timeout = 10s

; 当pm=dynamic时,指定可以生成的 PHP-FPM 子进程的最大数量,默认为0
process.max = 100

; 指定 PHP-FPM 子进程的优先级,范围-19-20,-19优先级别最高,默认不设置
process.priority = -10

; 是否开启守护进程,默认开启
daemonize = yes

; 设置fpm能打开的文件描述符数,默认值为操作系统默认值
rlimit_files= 65532

; 用于限制 PHP-FPM 在发生崩溃时可以生成的core dump文件的最大大小。默认为0不限制
rlimit_core = 100M

; 用于指定PHP-FPM使用的事件处理机制。
events.mechanism = epoll

; 用于控制 PHP-FPM 向 systemd 发送状态更新的频率。systemd 使用这些更新来监控 PHP-FPM 进程的健康状况并在必要时重新启动它。
systemd_interval = 10

; 进程池配置
[pool]

; 默认监听地址，接受FastCGI请求
; 可以是 Unix 套接字
listen = /run/php-fpm/php-fpm.sock
; 或 TCP/IP 地址
listen = 127.0.0.1:9000

; FPM进程全连接队列长度
; FreeBSD/OpenBSD默认值-1,意味着最大值
; Linux 默认值 511
listen.backlog = 1024

; IP白名单
listen.allowed_clients = 127.0.0.1, 192.168.1.0/24
listen.allowed_clients = *

; 设置FPM listen进程用户和用户组
listen.owner = www-data
listen.group = www-data
; 当使用unix.socket时的权限
listen.mode = 0660

; 设置FPM worker进程用户和用户组
user = www-data
group = www-data

; 设置FPM管理子进程的模式

; dynamic : 动态模式,推荐用这个
pm = dynamic 
; 动态模式下创建的最大子进程数量
pm.max_children = 100 
; 动态模式下初始子进程数量,默认值为min_spare_servers + (max_spare_servers - min_spare_servers) / 2
pm.start_servers = 10
; 动态模式下,当负载较低时保持至少10个空闲进程
pm.min_spare_servers = 10
; 动态模式下,当负载较高时保持最多20个空闲进程,工作中的子进程和空闲子进程的总数不超过pm.children
pm.max_spare_servers = 20
; 动态模式下生成自己子进程的速率,一次生成32个
pm.max_spawn_rate = 32

; static : 静态模式,子进程的数量是固定的
pm = static 
; 静态模式下固定10个子进程数量
pm.max_children = 10

; ondemand : 一次性模式,连接过来的时候再创建子进程
pm = ondemand
; 一次性模式模式下当子进程处理完毕,10s后删除,默认10s
pm.process_idle_timeout = 10s

;子进程处理500个请求之后重启,有效避免内存泄露,默认0
pm.max_requests = 500

; 启用 FPM 状态页面,可以查看当前运行的 PHP-FPM 工作进程的数量、内存使用情况、请求等待队列的长度等。此外，还可以通过状态页面杀死或终止正在运行的进程、查看请求详情以及进行其他诊断操作。默认没有开启
; 通过以下配置之后,浏览器直接访问http://127.0.0.1:9000/status即可
pm.status_listen = 127.0.0.1:9000
pm.status_path = /status

; 用于检测 FPM 进程是否存活,当 Web 服务器通过 FastCGI 协议向 FPM 发送带有 /ping 路径的请求时，FPM 将会响应pong。
ping.path = /ping
ping.path = pong

; 用于指定 PHP-FPM 运行时文件和其他文件的安装路径前缀。它的作用是将 PHP-FPM 安装路径与其他系统路径分离开来，从而方便管理和维护。
prefix = /usr/local/php-fpm


; 当子进程处理单个请求超时60秒,请求结束之后,子进程会被终止,当php.ini选项`max_execution_time`由于某种原因没有停止脚本执行时将使用这个配置。默认0不开启
request_terminate_timeout = 60s

; 当请求时间超过request_terminate_timeout时,如果配置开启,则立刻终止进程,自 PHP 7.3.0 启用。默认不开启
request_terminate_timeout_track_finished = yes

; 开启记录请求慢日志
slowlog = /usr/local/phpfpm/log/slow.log
; 超过10秒将被记入慢日志,默认0标识禁用慢日志功能
request_slowlog_timeout = 10s
; 记录调用栈的深度,默认20,PHP>=7.2.0
request_slowlog_trace_depth = 20

; 将 PHP-FPM 进程的根目录更改为指定目录。这意味着 PHP-FPM 进程将无法访问根目录之外的任何文件或目录。chroot 选项的值应该是一个绝对路径,默认不启用
chroot = /var/www

; 可以将 PHP-FPM 进程的当前工作目录更改为指定目录。这意味着 PHP-FPM 进程将在指定目录中运行，而不是在根目录中运行。
chdir = /var/www/example.com

; 是否将工作进程输出重定向到主进程日志。建议将其设置为yes，以便在出现问题时能够更好地调试。
catch_workers_output
; 是否在工作进程的日志中添加前缀。建议将其设置为no，以减少日志大小并减少I/O操作。PHP >= 7.3.0
decorate_workers_output

; 是否清除工作进程环境中的所有变量。建议将其设置为yes，以确保环境变量的一致性并增加安全性。
clear_env = yes
; 允许运行的脚本扩展名列表。建议将其设置为只包含必要的脚本扩展名，以增强安全性。
security.limit_extensions = .php .html

; 记录访问日志,默认不开启
access.log = /var/log/access.log
; 访问日志格式
access.format = %R - %u %t \"%m %r\" %s

; 设置环境变量,可通过getenv()获取 
; 设置 `HOSTNAME` 环境变量为主机名。
env[HOSTNAME] = $HOSTNAME
; 设置 `PATH` 环境变量为可执行文件的搜索路径。
env[PATH] = /usr/local/bin:/usr/bin:/bin`
; 设置 `TMP` 环境变量为临时文件夹的路径。
env[TMP] = /tmp
; 设置ENV变量为生产环境,可通过
if (getenv('ENV') == 'product') 加载不同配置文档
env[ENV] = product

; php_admin_value 可以设置任何PHP配置选项的值
; php_admin_flag 只能设置开/关型的选项。
; 两者将覆盖PHP.ini 配置文件
; disable_functions / disable_classes无法覆盖,只能追加
; ini_set()不能覆盖 php_admin_value/php_admin_flag
php_admin_value[upload_max_filesize] = 100M
php_admin_value[post_max_size] = 100M
php_admin_value[error_log] = /var/log/fpm-php.www.log
php_admin_flag[log_errors] = on
php_admin_value[memory_limit] = 32M
php_admin_value[disable_functions] = exec
```