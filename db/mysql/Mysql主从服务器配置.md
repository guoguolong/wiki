Mysql主从服务器配置

## I. 主MySQL服务器

### 1. 编辑 /etc/my.cnf

> [mysqld]
> server-id=1
> binlog-do-db=babyun_prod
> log_bin=/mnt/zeus/var/log/mysql/babyun_prod.log

重启MySQL服务器

### 2. 给从服务器设置复制访问权限，进入mysql控制台

> GRANT REPLICATION SLAVE ON *.* TO 'repl'@10.168.52.174 identified by 'Iai9063D';

### 3.锁定master数据库，避免binlog发生变化.

进入mysql控制台执行：

#### 1）锁表

> flush tables with read lock;

#### 2）查看主服务器状态

》show master status;

将file和position记下来，后面要用到

> +-----------------------+----------+------------------+---------------------+

| File | Position | Binlog_Do_DB | Binlog_Ignore_DB |

+-----------------------+----------+------------------+---------------------+

| mysql-bin.000001 | 106 | blog,blog2 | |

+-----------------------+----------+------------------+---------------------+

## II. 从MySQL服务器

### 1. 编辑 /etc/my.cnf

> [mysqld]
> server-id=2
> replicate-do-db=babyun_prod

重启MySQL服务器

### 2. 进入mysql控制台，连接主服务器并启动从服务器

#### 1). 使用如下命令设置连接主服务器的配置：

```
CHANGE MASTER TO
    MASTER_HOST='<主机>',
    MASTER_USER='<用户名>',
    MASTER_PASSWORD='<密码>',
    MASTER_LOG_FILE='<日志文件>',
    MASTER_LOG_POS=<文件位置>;
```

还记得前面在主服务器执行的show master status;命令吧，请使用File、Position列的值替换<日志文件>，<文件位置>。

从而具体执行该配置命令行为：

```
mysql>CHANGE MASTER TO
    MASTER_HOST='10.46.75.209',
    MASTER_USER='repl',
    MASTER_PASSWORD='Iai9063D',
    MASTER_LOG_FILE='babyun_prod.000001',
    MASTER_LOG_POS=106;
```

#### 2). 启动从服务器

> mysql>start slave;

#### 3). 查看从服务器状态（可选）

> mysql>show slave status\G 注意结果显示是否有错，比如 ：以下两项应该都是yes

> Slave_IO_Running : yes
> Slave_SQL_Running : yes

## III. 主MySQL服务器解锁

### 1. 登录主服务器进入mysql控制台，执行

> unlock table;

注：配置主从过程中遇到错误（主服务器log文件输出）

> 2015-11-30T02:42:43.431351Z 16 [Note] While initializing dump thread for slave with UUID
> <349d05ce-95d7-11e5-b709-00163e006d02>, found a zombie dump thread with the same UUID. Master is killing the zombie dump thread.
> 2015-11-30T02:42:43.431508Z 16 [Note] Start binlog_dump to master_thread_id(16) slave_server(21), pos babyun_prod.000023, 6166)

胡乱重启主、从服务器，stop/start slave, 重新配置CHANGE MASTER TO… 才成功.

典型的mysql 5.7配置如下：

```
[mysqld]
user        = mysql
pid-file    = /var/run/mysqld/mysqld.pid
socket      = /var/run/mysqld/mysqld.sock
port        = 3306
basedir     = /usr
datadir     = /mnt/zeus/var/lib/mysql
tmpdir      = /tmp
lc-messages-dir    = /usr/share/mysql
explicit_defaults_for_timestamp
character_set_server=utf8  
init_connect='SET NAMES utf8'  
skip-host-cache
skip-name-resolve
# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.

#bind-address    = 0.0.0.0
bind-address    = 10.46.75.209
log-error    = /var/log/mysql/error.log
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
# * IMPORTANT: Additional settings that can override those from this file!
#   The files must end with '.cnf', otherwise they'll be ignored.

sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
!includedir /etc/mysql/conf.d/
server-id=10
binlog-do-db=babyun_prod
log_bin=/mnt/zeus/var/log/mysql/babyun_prod.log
expire_logs_days        = 10
max_binlog_size         = 100M
binlog_format           =mixed
innodb_flush_log_at_trx_commit=1
sync_binlog=1
max_connections = 10000
max_length_for_sort_data=80960
sort_buffer_size=20097144
long_query_time = 1
slow_query_log = on
thread_cache_size=64 # 进程缓存，一般根据内存设置 1G => 8, 2G => 16, 3G => 32, >3G => 64
innodb_thread_concurrency=32 # 并发进程，一般为 CPU 核心数的两倍
innodb_buffer_pool_size=3G # 缓存innodb表的索引，数据，插入数据时的缓冲
innodb_log_buffer_size=36M
innodb_thread_concurrency=32
```