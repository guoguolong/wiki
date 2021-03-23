MySql 8二进制tar包安装

一般二进制安装参考：https://dev.mysql.com/doc/refman/8.0/en/binary-installation.html

### 1. 安装
    cd mysql
    chown mysql:mysql mysql-files
    chmod 750 mysql-files
    mkdir mysql-files
    ./bin/mysqld --initialize --user=mysql
    ./bin/mysql_ssl_rsa_setup -d=data

注：mac创建用户的方法比较繁琐，所以更建议用dmg文件安装

### 2. 修改mysql服务器密码
#### 不认证启动服务器
    ./bin/mysqld -nt --skip-grant-tables

#### 修改密码为空
    ./bin/mysql #进入mysql控制台

>mysql>use mysql;  
>mysql>update user set authentication_string='' where user='root';  
>mysql>update user set plugin="caching_sha2_password" where user='root'; # THIS LINE

### 3. 正常启动mysql服务器
./bin/mysqld_safe --user=mysql &