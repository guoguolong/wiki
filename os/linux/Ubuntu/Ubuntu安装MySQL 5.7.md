## 1.增加ppa条目（为了安装非默认版本）

```
add-apt-repository ppa:ondrej/mysql-5.7 
apg-get update
```

## 2. 安装Mysql 5.7

```
apt-get install mysql-server-5.7 
```

## 3. SHOW VARIABLES;命令可能返回的错误

不过当我进入MYSQL console执行SHOW VARIABLES; 显示：

```
'performance_schema.session_variables' doesn't exist
```

可能需要退出console执行

```
mysql_upgrade -u root -p --force
```

然后重启MySQL.

安装好mysql之后，通常要指定datadir到一个挂载的大硬盘 如：修改my.cnf的datadir目录为

> datadir = /mnt/zeus/var/lib/mysql

那么必须要修改sudo vim /etc/apparmor.d/usr.sbin.mysqld文件，指定目标目录白名单。

```
# Allow data dir access
  /var/lib/mysql/ r,
  /var/lib/mysql/** rwk,
  /mnt/zeus/var/lib/mysql/ r,
  /mnt/zeus/var/lib/mysql/** rwk,
 # 后面两行是新增行.
```