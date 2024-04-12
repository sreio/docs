> 对 Web 应用进行压力测试，一般使用 Apache 提供的压力测试工具 [ab](https://httpd.apache.org/docs/2.4/programs/ab.html) 。

ab 的功能非常强大，可以发起各种 HTTP 请求，并且支持设置 并发数 。测试完毕后，ab 还对测量数据进行统计分析，最终生成一份非常详细的测试报告。

那么，ab 工具如何使用呢？本文将演示 ab 若干用法，以此抛砖引玉。


## 安装

Web 应用一般部署在 Linux 服务器上，因此主流 Linux 发行版软件包都提供了 Apache 工具 ab 。以 Ubuntu 为例，我们只需按照 apache2-utils 包，即可得到 ab 命令：

```bash
$ sudo apt install apache2-utils
```

安装完毕后，我们可以通过 -V 选项查看 ab 的版本，确认它已经就绪：

```bash
$ ab -V
This is ApacheBench, Version 2.3 <$Revision: 1706008 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/
```

## 帮助文档
与大多数 Unix 命令一样，我们可以通过 -h 选项，查看 ab 命令的帮助文档：

```bash
$ ab -h
Usage: ab [options] [http[s]://]hostname[:port]/path
Options are:
    -n requests     Number of requests to perform
    -c concurrency  Number of multiple requests to make at a time
    -t timelimit    Seconds to max. to spend on benchmarking
                    This implies -n 50000
    -s timeout      Seconds to max. wait for each response
                    Default is 30 seconds
    -b windowsize   Size of TCP send/receive buffer, in bytes
    -B address      Address to bind to when making outgoing connections
    -p postfile     File containing data to POST. Remember also to set -T
    -u putfile      File containing data to PUT. Remember also to set -T
    -T content-type Content-type header to use for POST/PUT data, eg.
                    'application/x-www-form-urlencoded'
                    Default is 'text/plain'
    -v verbosity    How much troubleshooting info to print
    -w              Print out results in HTML tables
    -i              Use HEAD instead of GET
    -x attributes   String to insert as table attributes
    -y attributes   String to insert as tr attributes
    -z attributes   String to insert as td or th attributes
    -C attribute    Add cookie, eg. 'Apache=1234'. (repeatable)
    -H attribute    Add Arbitrary header line, eg. 'Accept-Encoding: gzip'
                    Inserted after all normal header lines. (repeatable)
    -A attribute    Add Basic WWW Authentication, the attributes
                    are a colon separated username and password.
    -P attribute    Add Basic Proxy Authentication, the attributes
                    are a colon separated username and password.
    -X proxy:port   Proxyserver and port number to use
    -V              Print version number and exit
    -k              Use HTTP KeepAlive feature
    -d              Do not show percentiles served table.
    -S              Do not show confidence estimators and warnings.
    -q              Do not show progress when doing more than 150 requests
    -l              Accept variable document length (use this for dynamic pages)
    -g filename     Output collected data to gnuplot format file.
    -e filename     Output CSV file with percentages served
    -r              Don't exit on socket receive errors.
    -m method       Method name
    -h              Display usage information (this message)
    -Z ciphersuite  Specify SSL/TLS cipher suite (See openssl ciphers)
    -f protocol     Specify SSL/TLS protocol
                    (TLS1, TLS1.1, TLS1.2 or ALL)
```

文档告诉我们，只需将 压测地址 传给 ab 并指定一些 参数选项 即可发起测试。常用的选项包括：

- `-n` ，指定压测 总请求数 ；
- `-c` ，指定 并发数 ，即同时发起的请求个数；
- `-s` ，请求 超时时间 ，默认是 30 秒；
- `etc`

## 报告解读

我在本地跑了一个用 Python 编写的 Web 服务，现在拿它来练练手：

```bash
$ ab -n 10000 -c 100 http://127.0.0.1:8080/
This is ApacheBench, Version 2.3 <$Revision: 1706008 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 127.0.0.1 (be patient)
Completed 1000 requests
Completed 2000 requests
Completed 3000 requests
Completed 4000 requests
Completed 5000 requests
Completed 6000 requests
Completed 7000 requests
Completed 8000 requests
Completed 9000 requests
Completed 10000 requests
Finished 10000 requests


Server Software:        Python/3.8
Server Hostname:        127.0.0.1
Server Port:            8080

Document Path:          /
Document Length:        19 bytes

Concurrency Level:      100
Time taken for tests:   5.972 seconds
Complete requests:      10000
Failed requests:        0
Total transferred:      1700000 bytes
HTML transferred:       190000 bytes
Requests per second:    1674.43 [#/sec] (mean)
Time per request:       59.722 [ms] (mean)
Time per request:       0.597 [ms] (mean, across all concurrent requests)
Transfer rate:          277.98 [Kbytes/sec] received

Connection Times (ms)
            min  mean[+/-sd] median   max
Connect:        0    2   1.5      1      15
Processing:    43   58   5.0     57      89
Waiting:       29   47   6.3     47      85
Total:         43   60   4.8     58      90

Percentage of the requests served within a certain time (ms)
50%     58
66%     59
75%     60
80%     61
90%     65
95%     69
98%     72
99%     85
100%     90 (longest request)
```

例子用 ab 对本地 Web 服务进行压力测试，压测总请求数 10000 ，并发数为 100 。

我们重点关注 ab 完成测试后生成的报告，首先是 统计数据 ：

- Time taken for tests ，测试 总耗时 ，为 5.972 秒；
- Complete requests ， 完成请求数 ，为 10000 ，也就是百分之百完成；
- Failed requests ， 失败请求数 ；
- Total transferred ，传输 数据总量 ， 170 万字节， 1.6MB 左右；
- HTML transferred ，传输 HTML 总量，也就是 HTTP 响应 Body 部分数据量；
- Requests per second ，每秒请求数 ；
- Time per request ， 请求平均耗时 ，第一个是真实耗时，不考虑并发数；
- Time per request ，第二个考虑并发数，刚好是第一个除以并发数；
- Transfer rate ，传输速率，277.98KB 每秒；

紧接着的是针对请求的 分阶段耗时统计 ，ab 将请求分为以下几个阶段：

- Connect ，连接阶段；
- Processing ，处理阶段；
- Waiting ，等待阶段；
- Total ，合计；

对于每个阶段耗时， ab 分别统计了 最小值 、 平均值 、中位数 以及 最大值 。

最后是请求 耗时分布 ，报告表明：

- 50% 的请求可以在 58 毫秒内完成；
- 66% 的请求可以在 59 毫秒内完成；
- 76% 的请求可以在 60 毫秒内完成；
- ……

## 典型用法

### POST发送表单

将需要发送的表单数据保存在一个文件中，假设文件名为 form.txt ，格式如下：

```bash
key=foo&value=123
```

指定 -p 选项让 ab 发送该文件；指定 -T 选项让 ab 设置正确的 Content-Type 头部：

```bash
$ ab -n 1 -c 1 -p form.txt -T application/x-www-form-urlencoded 'http://127.0.0.1:8080/test/json'
```

### POST发送JSON数据

将需要发送的 JSON 数据保存在一个文件中，假设文件名为 data.json ，格式如下：

```bash
{
    "key": "foo",
    "value": 123
}
```

指定 -p 选项让 ab 发送该文件；指定 -T 选项让 ab 设置正确的 Content-Type 头部：

```bash
$ ab -n 1 -c 1 -p data.json -T application/json 'http://127.0.0.1:8080/test/json'
```