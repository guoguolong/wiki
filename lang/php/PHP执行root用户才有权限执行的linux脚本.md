## PHP执行root用户才有权限执行的linux脚本

php执行linux的shell脚本，可用用passthru、exec等方法，但是如果命令是一个需要root权限才能执行的脚本，怎么办呢？

1. ps命令查询运行php-fpm进程的用户（一般是apche）
2. root用户登录执行visudo，追加：

```
apache ALL=NOPASSWD:/bin/sh
```

表示apache可以用sudo命令，并且不用输入密码

1. 如果原来php执行的命令为

```
exec('/projects/script/run.sh'); 
```

那么现在应修改为：

```
exec('sudo sh /projects/script/run.sh');
```