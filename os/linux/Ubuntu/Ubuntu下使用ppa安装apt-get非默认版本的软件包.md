首先要学习一下ppa的基本概念https://launchpad.net 以安装php非默认版本为例：先去launchpad.net搜索php对应的ppa串，我搜的结果是：ppa:ondrej/php root身份或sudo进入ubuntu，执行

```
apt-get install python-software-properties -y
add-apt-repository ppa:ondrej/php # 可能提示你php 5.5, php5.6, php7.0，意味这些包都出现在仓库里，需要你选择安装具体的一个
apt-get update
```

更明确地搜索ppa包括，用如下url（搜索unbunt下的redis） https://launchpad.net/ubuntu/+ppas?name_filter=redis 打开搜索到的包的主页，会给出ppa字符串或者deb/deb-src命令全串添加仓库.

## 接下来你想安装php5.6

```
apt-get install php5.6 php5.6-dev # -dev包括了phpize等工具
apt-get install php5.6-fpm #和nginx对接
```

启动php-fpm

```
service php5.6-fpm restart
```

顺便说下nginx+ php-fpm方式运行php

```
apt-get install php5.6-fpm 
```

php-fpm的listen与nginx listen方式需要相同 要么都是listen = 127.0.0.1:9000 ，要么都是用sock文件