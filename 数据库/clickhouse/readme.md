
# 单节点-通过二进制安装

# 安装&使用&配置
> `官方安装:` https://clickhouse.com/docs/getting-started/quick-start
```shell

# 1.  下载官方安装脚本
curl https://clickhouse.com/ | sh

sudo ./clickhouse install

> Set up the password for the default user: [设置default密码]
>
> Allow server to accept connections from the network (default is localhost only), [y/N]  允许服务器接受网络连接（默认是本地服务器）
>

# 启动服务
sudo clickhouse start
# 重启服务
sudo clickhouse restart
# 帮助(命令大全)
sudo clickhouse help
# 日志目录
sudo ls -alh /var/log/clickhouse-server/
# 错误日志
sudo tail -f /var/log/clickhouse-server/clickhouse-server.err.log
# 正常日志
sudo tail -f /var/log/clickhouse-server/clickhouse-server.log
# 登录客户端
clickhouse-client --password

# 数据路径
> /var/lib/clickhouse 
# 配置路径
> sudo vim /etc/clickhouse-server/config.xml
```


## 查看数据大小
```shell
## 查看所有库表大小
select
    sum(rows) as row,--总行数
    formatReadableSize(sum(data_uncompressed_bytes)) as ysq,--原始大小
    formatReadableSize(sum(data_compressed_bytes)) as ysh,--压缩大小
    round(sum(data_compressed_bytes) / sum(data_uncompressed_bytes) * 100, 0) ys_rate--压缩率
from system.parts


## 查看device库表大小
select
    database,
    table,
    formatReadableSize(size) as size,
    formatReadableSize(bytes_on_disk) as bytes_on_disk,
    formatReadableSize(data_uncompressed_bytes) as data_uncompressed_bytes,
    formatReadableSize(data_compressed_bytes) as data_compressed_bytes,
    compress_rate,
    rows,
    days,
    formatReadableSize(avgDaySize) as avgDaySize
from
(
    select
        database,
        table,
        sum(bytes) as size,
        sum(rows) as rows,
        min(min_date) as min_date,
        max(max_date) as max_date,
        sum(bytes_on_disk) as bytes_on_disk,
        sum(data_uncompressed_bytes) as data_uncompressed_bytes,
        sum(data_compressed_bytes) as data_compressed_bytes,
        (data_compressed_bytes / data_uncompressed_bytes) * 100 as compress_rate,
        max_date - min_date as days,
        size / (max_date - min_date) as avgDaySize
    from system.parts
    where active 
     and database = 'device'
    group by
        database,
        table
)

# 查看system库表大小
SELECT
    database,
    `table`,
    formatReadableSize(size) AS size,
    formatReadableSize(bytes_on_disk) AS bytes_on_disk,
    formatReadableSize(data_uncompressed_bytes) AS data_uncompressed_bytes,
    formatReadableSize(data_compressed_bytes) AS data_compressed_bytes,
    compress_rate,
    rows,
    days,
    formatReadableSize(avgDaySize) AS avgDaySize
FROM
(
    SELECT
        database,
        `table`,
        sum(bytes) AS size,
        sum(rows) AS rows,
        min(min_date) AS min_date,
        max(max_date) AS max_date,
        sum(bytes_on_disk) AS bytes_on_disk,
        sum(data_uncompressed_bytes) AS data_uncompressed_bytes,
        sum(data_compressed_bytes) AS data_compressed_bytes,
        (data_compressed_bytes / data_uncompressed_bytes) * 100 AS compress_rate,
        max_date - min_date AS days,
        size / (max_date - min_date) AS avgDaySize
    FROM system.parts
    WHERE active AND (database = 'system')
    GROUP BY
        database,
        `table`
)
```



```shell
ALTER TABLE system.processors_profile_log MODIFY TTL event_date + INTERVAL 3 DAY;
ALTER TABLE system.part_log MODIFY TTL event_date + INTERVAL 3 DAY;
ALTER TABLE system.query_metric_log MODIFY TTL event_date + INTERVAL 3 DAY;
ALTER TABLE system.metric_log MODIFY TTL event_date + INTERVAL 3 DAY;
ALTER TABLE system.query_log MODIFY TTL event_date + INTERVAL 3 DAY;
ALTER TABLE system.asynchronous_metric_log MODIFY TTL event_date + INTERVAL 3 DAY;
ALTER TABLE system.text_log MODIFY TTL event_date + INTERVAL 3 DAY;
ALTER TABLE system.latency_log MODIFY TTL event_date + INTERVAL 3 DAY;
ALTER TABLE system.error_log MODIFY TTL event_date + INTERVAL 3 DAY;
ALTER TABLE system.trace_log MODIFY TTL event_date + INTERVAL 3 DAY;

```
