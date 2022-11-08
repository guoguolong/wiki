## 进入psql环境
```bash
psql -h 192.168.3.218  -p 5432  -U postgres
```

## DML命令

```postgresql
psql (14.4)
Type "help" for help.

cn-allen=# create user xxx with password 'xxx';  # 创建账户
CREATE ROLE
cn-allenguo=# create database poseidon owner allen; # 创建数据库
CREATE DATABASE
cn-allenguo=# grant all privileges on database poseidon to allen; # 把所有权限赋予给allen（此处不必，因为allen本身就是 owner）
GRANT
cn-allenguo=# \l # 查看数据库
                                    List of databases
    Name     |    Owner    | Encoding | Collate | Ctype |        Access privileges        
-------------+-------------+----------+---------+-------+---------------------------------
 cn-allenguo | cn-allenguo | UTF8     | C       | C     | 
 poseidon    | allen       | UTF8     | C       | C     | =Tc/allen                      +
             |             |          |         |       | allen=CTc/allen
 postgres    | cn-allenguo | UTF8     | C       | C     | 
 template0   | cn-allenguo | UTF8     | C       | C     | =c/"cn-allenguo"               +
             |             |          |         |       | "cn-allenguo"=CTc/"cn-allenguo"
 template1   | cn-allenguo | UTF8     | C       | C     | =c/"cn-allenguo"               +
             |             |          |         |       | "cn-allenguo"=CTc/"cn-allenguo"
 wiki        | wikijs      | UTF8     | C       | C     | 
(6 rows)
cn-allenguo=# \c poseidon # 进入数据库
You are now connected to database "poseidon" as user "cn-allenguo".
cn-allenguo=# \dt #查看表
cn-allenguo=# \c interface #切换数据库
cn-allenguo=# \d #查看某个库中的某个表结构
cn-allenguo=# \encoding #显示字符集
cn-allenguo=# 
cn-allenguo-# \q # 退出 
```


