## supervisor简介

`Supervisor` 是一个客户端/服务器系统，它允许其用户监视和控制类 `UNIX` 操作系统上的许多进程,用`Python`开发的一套通用的进程管理程序。它是通过`fork/exec`的方式把这些被管理的进程当作`supervisor`的子进程来启动，只要在`supervisor`的配置文件中，把要管理的进程的可执行文件的路径写进去，就可以进行管理。具体文档参考[supervisord官网](http://supervisord.org/)

## 安装

<!-- tabs:start -->
#### **pip**

> pip需要安装python环境 [平台要求](http://supervisord.org/introduction.html#platform-requirements)
>
> Supervisor 适用于 Python 3.4 或更高版本以及 Python 2.7 版本。

```terminal
pip install supervisor
#安装supervisor默认是没有生成配置文件的，生成配置文件
echo_supervisord_conf > /etc/supervisord.conf
```
#### **centos**
```terminal
#使用yum安装supervisor
yum install supervisor
systemctl enable supervisord.service  
```

#### **ubuntu/debian**
```terminal
# 使用apt安装supervisor
apt-get install supervisor
```
<!-- tabs:end -->


## 配置文件

### 主配置文件： supervisord.conf

可以通过运行`echo_supervisord_conf`获得。这个配置文件一般情况下不需要更改，除了最后的[include]部分，其余保持默认即可。

`supervisor`的配置文件是`/etc/supervisord.conf`，默认配置文件是`/etc/supervisord.conf`，可以通过`-c`参数指定配置文件路径。

```editorconfig
[unix_http_server]
file=/tmp/supervisor.sock   ;UNIX socket 文件，supervisorctl用XML_RPC和supervisord通信就是通过它进行，如果不设置的话，supervisorctl也就不能用了 
;chmod=0700                 ;socket文件的mode(权限)，默认是0700
;chown=nobody:nogroup       ;socket文件的owner(属组)，格式：uid:gid
;username=user              ;使用supervisorctl连接的时候，认证的用户（默认没有用户名）
;password=123               ;和上面的用户名对应的密码，可以直接使用明码，也可以使用SHA加密（默认没有密码）
;[inet_http_server]         ;侦听在TCP上的socket，Web Server和远程的supervisorctl都要用到它不设置的话，默认为不开启。
;port=127.0.0.1:9001        ;这个是管理后台运行的IP和端口，侦听所有IP用:9001或*:9001。如果上面的[inet_http_server]开启了，就必须设置它。如果开放到公网，需要注意安全性
;username=user              ;同上面的[unix_http_server]用户名配置一致
;password=123               ;同上面的[unix_http_server]密码配置一致
 [supervisord]            ;这个主要是定义supervisord这个服务端进程的一些参数的
logfile=/tmp/supervisord.log ;这个是supervisord这个主进程的日志路径，注意和子进程的日志没有关系。默认是 $CWD/supervisord.log，$CWD是当前目录。
logfile_maxbytes=50MB        ;日志文件大小，默认50MB。超过50M的时候，会生成一个新的日志文件；如果设成0，表示不限制大小。
logfile_backups=10           ;日志文件保留备份数量默认10，设为0表示不备份
loglevel=info                ;日志级别，默认info，其它: critical, error, warn, info, debug, trace, or blather
pidfile=/tmp/supervisord.pid ;pid 文件
nodaemon=false               ;是否在前台启动，默认是false，即以 daemon 的方式启动
minfds=1024                  ;可以打开的文件描述符的最小值，默认 1024，低于这个值supervisor将不会启动。
minprocs=200                 ;可以打开的进程数的最小值，默认200，低于这个值supervisor也将不会正常启动。
;umask=022                   ;进程创建文件的掩码,默认为022。
;user=chrism                 ; 这个参数可以设置一个非root用户，当我们以root用户启动supervisord之后。我这里面设置的这个用户，也可以对supervisord进行管理,默认情况是不设置。
;identifier=supervisor       ;这个参数是supervisord的标识符，主要是给XML_RPC用的。当你有多个supervisor的时候，而且想调用XML_RPC统一管理，就需要为每个supervisor设置不同的标识符,默认是supervisord。
;directory=/tmp              ;这个参数是当supervisord作为守护进程运行的时候，设置这个参数的话，启动supervisord进程之前，会先切换到这个目录默认不设置。
;nocleanup=true              ;这个参数当为false的时候，会在supervisord进程启动的时候，把以前子进程产生的日志文件(路径为AUTO的情况下)清除掉。有时候咱们想要看历史日志，当 然不想日志被清除了。所以可以设置为true，默认是false，有调试需求的同学可以设置为true。
;childlogdir=/tmp            ;当子进程日志路径为AUTO(系统自动分配)的时候，子进程日志文件的存放路径。
;environment=KEY="value"     ;这个是用来设置环境变量的，supervisord在linux中启动默认继承了linux环境变量，在这里可以设置supervisord进程特有的其他环境变量。supervisord启动子进程时，子进程会拷贝父进程的内存空间内容。 所以设置的这些环境变量也会被子进程继承。默认为不设置。
;strip_ansi=false            ;这个选项如果设置为true，会清除子进程日志中的所有ANSI 序列。什么是ANSI序列呢？就是我们的\n,\t这些东西。默认为false。

[supervisorctl]              ;这个主要是针对supervisorctl的一些配置  
serverurl=unix:///tmp/supervisor.sock ;通过UNIX socket连接supervisord，路径与unix_http_server部分的file一致
;serverurl=http://127.0.0.1:9001 ; 通过HTTP的方式连接supervisord
;username=chris              ;用户名，默认空
;password=123                ;密码，默认空。
;prompt=mysupervisor         ;输入用户名密码时候的提示，默认supervisor。                   
;history_file=~/.sc_history  ;这个参数和shell中的history类似，我们可以用上下键来查找前面执行过的命令默认是no file的。所以我们想要有这种功能，必须指定一个文件。

;[program:xx]             ;是被管理的进程配置参数，xx是进程的名称
;command=/opt/apache-tomcat-8.0.35/bin/catalina.sh run  ;程序启动命令;可以带参数,如：/home/test.py -a 'hehe'有一点需要注意的是，我们的command只能是那种在终端运行的进程，不能是守护进程。这个想想也知道了，比如说command=service httpd start。httpd这个进程被linux的service管理了，我们的supervisor再去启动这个命令，这已经不是严格意义的子进程了。
;directory=/tmp                ;进程运行前，会前切换到这个目录
;autostart=true              ;在supervisord启动的时候也自动启动
;startsecs=10              ;启动10秒后没有异常退出，就表示进程正常启动了，默认为1秒。
;autorestart=true          ;程序退出后自动重启,可选值：[unexpected,true,false]，默认为unexpected，表示进程意外杀死后才重启 这个是设置子进程挂掉后自动重启的情况，如果为false的时候，无论什么情况下，都不会被重新启动，如果为unexpected，只有当进程的退出码不在下面的exitcodes里面定义的退出码的时候，才会被自动重启。当为true的时候，只要子进程挂掉，将会被无条件的重启。                               
;exitcodes=0,2               ;注意和上面的的autorestart=unexpected对应。exitcodes里面的定义的退出码是expected的。
;stopsignal=QUIT             ;进程停止信号，可以为TERM, HUP, INT, QUIT, KILL, USR1, or USR2等信号。默认为TERM 。当用设定的信号去干掉进程，退出码会被认为是expected。    
;stopwaitsecs=10             ;这个是当我们向子进程发送stopsignal信号后，到系统返回信息给supervisord，所等待的最大时间。 超过这个时间，supervisord会向该子进程发送一个强制kill的信号。 默认为10秒。
;startretries=3            ;启动失败自动重试次数，默认是3。当超过3次后，supervisor将把此进程的状态置为FAIL
;user=tomcat               ;用哪个用户启动进程，默认是root。如果supervisord是root启动，我们在这里设置这个非root用户，可以用来管理该program。
;priority=999              ;进程启动优先级，默认999，值小的优先启动
;redirect_stderr=true ;把stderr重定向到stdout，默认false
;stdout_logfile_maxbytes=20MB ;stdout日志文件大小，默认50MB
;stdout_logfile_backups = 20  ;stdout日志文件备份数，默认是10
;stdout_logfile=/opt/apache-tomcat-8.0.35/logs/catalina.out ;进程的stdout的日志路径，可以指定路径，AUTO，none等三个选项。设置为none的话，将没有日志产生。设置为AUTO的话，将随机找一个地方生成日志文件，而且当supervisord重新启动的时候，以前的日志文件会被清空。当 ;redirect_stderr=true的时候，sterr也会写进这个日志文件。需要注意当指定目录不存在时无法正常启动，所以需要手动创建目录（supervisord 会自动创建日志文件）
;stopasgroup=false         ;默认为false,进程被杀死时，是否向这个进程组发送stop信号，包括子进程。主要用于，supervisord管理的子进程，这个子进程本身还有子进程。那么我们如果仅仅干掉supervisord的子进程的话，子进程的子进程有可能会变成孤儿进程。所以咱们可以设置可个选项，把整个该子进程的整个进程组都干掉。 设置为true的话，一般killasgroup也会被设置为true。需要注意的是，该选项发送的是stop。默认为false。
;killasgroup=false             ;信号默认为false，不过向进程组发送的是kill信号
;stderr_capture_maxbytes=1MB   ;这个一样，和stdout_capture一样。 默认为0，关闭状态
;stderr_events_enabled=false   ;这个也是一样，默认为false
;environment=A="1",B="2"       ;这个是该子进程的环境变量，和别的子进程是不共享的
;serverurl=AUTO    

;[group:thegroupname]  ;就是给programs(子进程)分组，划分到组里面的program。我们就不用一个一个去操作了，我们可以对组名进行统一的操作。 注意：program被划分到组里面之后，就相当于原来的配置从supervisor的配置文件里消失了。supervisor只会对组进行管理，而不再会对组里面的单个program进行管理了
;programs=progname1,progname2 ;组成员，用逗号分开。
;priority=999                  ;优先级，相对于组和组之间说的。默认999。
 ;包含其它配置文件 ;当我们要管理的进程很多的时候，都写在主配置文件就不太合适了，这个时候可以把配置信息写到多个文件中，然后include过来
[include]
; .ini和.conf都支持
files = relative/directory/*.ini    ;可以指定一个或多个以.ini结束的配置文件
```

### 子进程配置文件

```editorconfig
;*为必须填写项
;*[program:应用名称]
[program:cat]

;*命令路径,如果使用python启动的程序应该为 python /home/test.py, 
;不建议放入/home/user/, 对于非user用户一般情况下是不能访问
command=/bin/cat

;当numprocs为1时,process_name=%(program_name)s;
;当numprocs>=2时,%(program_name)s_%(process_num)02d
process_name=%(program_name)s

;进程数量
numprocs=1

;执行目录,若有/home/supervisor_test/test1.py
;将directory设置成/home/supervisor_test
;则command只需设置成python test1.py
;否则command必须设置成绝对执行目录
directory=/tmp

;掩码:--- -w- -w-, 转换后rwx r-x w-x
umask=022

;优先级,值越高,最后启动,最先被关闭,默认值999
priority=999

;如果是true,当supervisor启动时,程序将会自动启动
autostart=true

;*自动重启
autorestart=true

;启动延时执行,默认1秒
startsecs=10

;启动尝试次数,默认3次
startretries=3

;当退出码是0,2时,执行重启,默认值0,2
exitcodes=0,2

;停止信号,默认TERM
;中断:INT(类似于Ctrl+C)(kill -INT pid),退出后会将写文件或日志(推荐)
;终止:TERM(kill -TERM pid)
;挂起:HUP(kill -HUP pid),注意与Ctrl+Z/kill -stop pid不同
;从容停止:QUIT(kill -QUIT pid)
;KILL, USR1, USR2其他见命令(kill -l),说明1
stopsignal=TERM

stopwaitsecs=10

;*以root用户执行
user=root

;重定向
redirect_stderr=false

stdout_logfile=/a/path
stdout_logfile_maxbytes=1MB
stdout_logfile_backups=10
stdout_capture_maxbytes=1MB
stderr_logfile=/a/path
stderr_logfile_maxbytes=1MB
stderr_logfile_backups=10
stderr_capture_maxbytes=1MB

;环境变量设置
environment=A="1",B="2"

serverurl=AUTO
```


## supervisord 和 supervisorctl

### supervisord
> `supervisord 是主进程。`
>
> supervisord 是 supervisor 的服务端程序。启动 supervisor 程序自身，启动 supervisor 管理的子进程，响应来自 clients 的请求，
重启闪退或异常退出的子进程，把子进程的 stderr 或 stdout 记录到日志文件中，生成和处理 Event。

通过`supervisord -h`可以查看帮助说明。示例：
```terminal
-c/--configuration FILENAME ;指定配置文件
-n/--nodaemon ;运行在前台（调试用）
-v/--version ;打印版本信息

-u/--user USER ;以指定用户（或用户ID）运行
-m/--umask UMASK ;指定子进程的umask，默认是022
-l/--logfile FILENAME ;指定日志文件
-e/--loglevel LEVEL ;指定日志级别
```


### supervisorctl
> `supervisorctl 是客户端程序，用于向supervisord发起命令。`
>
> 如果说 supervisord 是 supervisor 的服务端程序，那么 supervisorctl 就是 client 端程序了。
supervisorctl 有一个类型 shell 的命令行界面，可以利用它来查看子进程状态，启动 / 停止 / 重启子进程，
获取 running 子进程的列表等等。最重要的是，supervisorctl 不仅可以连接到本机上的 supervisord，
还可以连接到远程的 supervisord，当然在本机上面是通过 UNIX socket 连接的，远程是通过 TCP socket 连接的。
supervisorctl 和 supervisord 之间的通信，是通过 xml_rpc 完成的。


通过`supervisorctl -h`可以查看帮助说明。我们主要关心的是其`action`命令：
```terminal
$ supervisorctl  help

default commands (type help <topic>):
=====================================
add    exit      open  reload  restart   start   tail   
avail  fg        pid   remove  shutdown  status  update 
clear  maintail  quit  reread  signal    stop    version
```
这些命令对于控制子进程非常重要。示例:

```terminal
reread ;重新加载配置文件
update ;将配置文件里新增的子进程加入进程组，如果设置了autostart=true则会启动新新增的子进程
status ;查看所有进程状态
status <name> ;查看指定进程状态
start all; 启动所有子进程
start <name>; 启动指定子进程
restart all; 重启所有子进程
restart <name>; 重启指定子进程
stop all; 停止所有子进程
stop <name>; 停止指定子进程
reload ;重启supervisord
add <name>; 添加子进程到进程组
reomve <name>; 从进程组移除子进程，需要先stop。注意：移除后，需要使用reread和update才能重新运行该进程
```
supervisord 有进程组(process group)的概念：只有子进程在进程组，才能被运行。

**supervisorctl也支持交互式命令行：**
```terminal
$ supervisorctl
echo_time                        RUNNING   pid 27188, uptime 0:05:09
supervisor> version
3.3.4
supervisor> 
```


!> 注： supervisord是主进程，supervisorctl是给守护进程发送命令的客户端工具。


## 命令列表

### 查看安装的版本
```terminal
$ supervisord -v
3.3.4
```

### 查看supervisor的状态
```terminal
supervisorctl  status
```

### 指定主配置文件，则需要使用-c参数
```terminal
$ supervisord -c /etc/supervisor/supervisord.conf
```

### 重启 supervisor
```terminal
supervisorctl reload   
```

### 启动/停止/查看名字为 blog 的 program
```terminal
supervisorctl start blog
supervisorctl stop blog
supervisorctl status blog
```

### 启动/停止/查看 所有的 program
```terminal
supervisorctl start all
supervisorctl stop all
supervisorctl status all
```

### 重新读取配置
```terminal
sudo supervisorctl reread
```

### 更新配置
```terminal
sudo supervisorctl update
```


## 使用示例

我们先修改`supervisord.conf`最后的[include]部分配置：
```editorconfig
[include]
files = /etc/supervisor/conf.d/*.conf
```
这样就可以支持子配置文件，而不用改动主配置文件。


我们以简单的 `/tmp/echo_time.sh` 为例：

```bash
#/bin/bash

while true; do
    echo `date +%Y-%m-%d,%H:%m:%s`
    sleep 2
done
```

在`/etc/supervisor/conf.d/`新增子进程配置文件 `echo_time.conf`：

```editorconfig
[program:echo_time]
command=sh /tmp/echo_time.sh
priority=999                ; the relative start priority (default 999)
autostart=true              ; start at supervisord start (default: true)
autorestart=true            ; retstart at unexpected quit (default: true)
startsecs=10                ; number of secs prog must stay running (def. 10)
startretries=3              ; max # of serial start failures (default 3)
exitcodes=0,2               ; 'expected' exit codes for process (default 0,2)
stopsignal=QUIT             ; signal used to kill process (default TERM)
stopwaitsecs=10             ; max num secs to wait before SIGKILL (default 10)
user=root                 ; setuid to this UNIX account to run the program
log_stdout=true
log_stderr=true             ; if true, log program stderr (def false)
logfile=/tmp/echo_time.log
logfile_maxbytes=1MB        ; max # logfile bytes b4 rotation (default 50MB)
logfile_backups=10          ; # of logfile backups (default 10)
stdout_logfile_maxbytes=20MB  ; stdout 日志文件大小，默认 50MB
stdout_logfile_backups=20     ; stdout 日志文件备份数
stdout_logfile=/tmp/echo_time.stdout.log
```

然后启动程序：

```terminal
$ supervisorctl reread
$ supervisorctl update
```

这两个命令分别代表重新读取配置、更新子进程组。执行update后输出：

```terminal
echo_time: added process group
```

这样刚才添加的echo_time脚本就常驻运行起来了。可以通过日志查看运行情况：

```terminal
$ tail -f /tmp/echo_time.stdout.log
2024-02-22,14:12:1545459550
2024-02-22,14:12:1545459552
2024-02-22,14:12:1545459554
```

也可以使用`supervisorctl status`查看子进程运行情况：

```terminal
$ supervisorctl  status
echo_time                        RUNNING   pid 28206, uptime 0:00:11
```

