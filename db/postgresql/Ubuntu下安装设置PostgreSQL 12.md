## I. 安装PostgreSQL

这里是 Ubuntu 下安装 PostgreSQL（https://www.postgresql.org/download/linux/ubuntu/ ），简单明了，总结如下：

### 1. 创建 /etc/apt/sources.list.d/pgdg.list

```
sudo vim /etc/apt/sources.list.d/pgdg.list
```

内容如下：

```
deb http://apt.postgresql.org/pub/repos/apt/ bionic-pgdg main
```

在我的 Ubuntu 中，bionic 是当前系统版本名。你要根据自己的系统版本 YOUR_UBUNTU_VERSION_HERE 做相应替换。

> 我是怎么找到这个版本名的？
> cat /etc/apt/sources.list.d/nodesource.list，里面找到 bionic

### 2. 导入 repository signing key

```
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
```

### 3. 更新包列表

```
sudo apt-get update
```

### 4. 安装 PostgreSQL

```
sudo apt install postgresql-12
```

## II. 启停 PostgreSQL 服务器

安装好之后，默认已经启动 PostgreSQL，默认启动在 5432 端口

```
sudo systemctl start postgresql.service
sudo systemctl stop postgresql.service
sudo systemctl restart postgresql.service
```

技巧：可以通过 `systemctl status postgresql`查看服务名

## III. 访问 PostgreSQL 服务器

psql 是默认的命令行客户端软件。apt 安装后创建了名为 postgres 的系统用户，具有访问psql的超级权限

### 1. 以超级用户身份访问数据库

```
sudo -u postgres psql
```

可以修改 postgres 用户的密码，参考： http://note.youdao.com/noteshare?id=4631344252b1c49f7e8c6c337f9f419a

### 2. 创建用户访问指定数据库为Owner

#### a). 创建用户 magicmooc

```
postgres=# CREATE USER magicmooc WITH PASSWORD 'magicmooc';
```

#### b). 创建数据库 moocdb

```
postgres=# CREATE DATABASE moocdb OWNER magicmooc;
```

#### c). 将 moocdb 数据库的所有权限都赋予 magicmooc 用户

```
postgres=# GRANT ALL PRIVILEGES ON DATABASE moocdb TO magicmooc
```

输入 \q 退出 psql。

### 3. 以新建用户 magicmooc 执行 psql（方式I：md5 认证）

```
psql -h localhost -U magicmooc moocdb
```

输入密码，进入 psql。

> 注：一定要用 -h 指定 localhost 参数。

### 4. 以新建用户 magicmooc 执行 psql（方式II：peer 认证）

可用前文 postgres 系统用户（即 peer 认证）进入 psql，方法如下：

#### a). 创建Linux普通用户 magicmooc

> $ sudo adduser magicmooc

#### b). 以 peer 认证方式进入 psql

```
sudo -u magicmooc psql -d moocdb
```

## IV. psql 命令集

### 数据库操作

```
\l  #查看数据库
\c moocdb  # 进入名字为 moocdb 的数据库
drop database magicmooc   # 删除名字为 magicmooc 的数据库
```

### 表操作

```
\dt # 查看数据库中所有表
\d <表名> # 查看表名
```

## V. 导入导出SQL

### a). 导出 SQL 语句

使用 --column-inserts 参数的 pg_dump 命令

md5认证方式：

```
pg_dump -h localhost -U magicmooc  --column-inserts moocdb> mooc.sql 
```

peer认证方式：

```
sudo -u magicmooc pg_dump -d moocdb  --column-inserts > mooc.sql
```

### b). 导入 SQL 语句

md5认证方式：

```
psql -h localhost -U magicmooc moocdb < ./mooc.sql
```

peer认证方式：

```
sudo -u magicmooc psql -d moocdb < ./mooc.sql
```

导入时候，可能出现错误

```
CREATE EXTENSION
ERROR:  must be owner of extension plpgsql
```

解决方法是，进入 moocdb：`sudo -u magicmooc psql -d moocdb`，然后赋予 magicmooc 合适的角色

#### （1） 查看所有用户的权限

```
moocdb2=# \du
```

结果显示 magicmooc没有设置任何角色

```
                                   List of roles
 Role name |                         Attributes                         | Member of 
-----------+------------------------------------------------------------+-----------
 magicmooc |                                                            | {}
 postgres  | Superuser, Create role, Create DB, Replication, Bypass RLS | {}
```

#### （2）然后赋予 magicmooc 用户 SUPERUSER 的角色

```
ALTER USER magicmooc SUPERUSER;
```

好了，`\q` 退出 psql，重新执行导入命令即可

## VI. PostgreSQL开启远程连接

需要修改两个文件：

- pg_hba.conf
- postgresql.conf

### 1. 修改pg_hba.conf文件

在IPv4下面添加：`host all all 192.168.1.0/24 md5`

```
# IPv4 local connections:
host    all     all     127.0.0.1/32            trust
host    all     all     192.168.1.0/24          md5
```

在文件最下面添加

```
host    all     all     0.0.0.0/0      md5
```

### 2. 修改postgresql.conf

```
#listen_addresses = 'localhost'
```

将上面行去掉注释(#)，localhost 改成 *

```
listen_addresses = '*'
```

### 3.重启postgresql服务