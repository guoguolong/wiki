## 1. 停止MySql

> service mysql stop

## 2. --skip-grant-tables启动MySql

> mysqld_safe --skip-grant-tables &

## 3. 连接MySql服务器

> mysql -p

更新密码

```
use mysql;
update user set authentication_string=PASSWORD("") where User='root';

update user set plugin="mysql_native_password"; where user='root'; # THIS LINE

flush privileges;
quit;
```

## 4. 重新启动MySql