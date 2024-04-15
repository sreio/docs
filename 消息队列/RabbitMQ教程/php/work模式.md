## 1.前置教程
请先阅读下面章节，了解相关知识

- RabbitMQ基础概念
- RabbitMQ Work模式
- RabbitMQ PHP快速入门章节 （必读，因为后续章节不会重复贴代码，仅展示关键代码）

## 2.PHP实现多个消费者
PHP因为自己本身不支持多线程、协程之类的并发技术，通常使用多进程方式实现并发处理，这里使用多进程模式实现多个消费者并发消费队列内的消息。

### 2.1.手动启动多个进程
要实现多进程，最简单的办法就是手动运行多次PHP命令即可。
例如：
上一个章节中的消费者脚本为：recv.php

我们可以打开多个shell窗口重复执行消费者脚本即可

```terminal
# 下面启动两个消费者
# shell窗口1
php recv.php

# shell窗口2
php recv.php
```
也可以这样:
```terminal
# shell窗口1
php recv.php &
php recv.php &
```
在同一个shell窗口内，将脚本放到后台运行

> 说明：这种手动启动多个php脚本的方式实现多个消费者，缺点就是进程不好维护，没有进程监控，如果进程挂了，不会自动重启。

## 2.2.Supervisor实现多进程
Supervisor 是 Linux 操作系统下中的一个进程监控器，通过Supervisor可以监控php进程，如果php进程挂了会自动重启，也可以配置进程的并发数，这样可以轻松实现多个消费者并发处理消息。

下面以Ubuntu为例，其他Linux类似

安装supervisor
```terminal
sudo apt-get install supervisor
```
配置 Supervisor
Supervisor 的配置文件通常位于 /etc/supervisor/conf.d 目录下。在该目录中，你可以创建任意数量的配置文件，用来告诉supervisor 怎么监控我们的进程。例如，创建一个rabbitmq-worker.conf 文件, 用来监控我们的消费者进程。

例子:
文件: rabbitmq-worker.conf
```conf
[program:rabbitmq-worker]
process_name=%(program_name)s_%(process_num)02d
command=php recv.php
autostart=true
autorestart=true
user=root
numprocs=10
redirect_stderr=true
stdout_logfile=/var/log/worker.log
```
参数说明：

- `process_name` - 进程名字定义，可以随便命名, 这里用了两个变量program_name(进程名)和process_num（进程编号）
- `command` - 我们需要运行的命令
- `autostart` - 是否开机启动
- `autorestart` - 是否自动重启
- `user` - 使用哪个系统账号运行命令
- `numprocs` - 进程并发数，打算启动几个进程
- `stdout_logfile` - 运行日志文件保存在哪里