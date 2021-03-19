## brew 安装了啥

```
brew install tree
brew install nginx
brew install git-lfs
brew install wget
brew install node
brew install redis
brew install memcached
brew install php
brew install mysql
brew install mongodb
```

## 关于PHP安装(19年7月)

### 安装PHP (7.3.7)

```
brew install php
```

### 启动php-fpm

```
brew services restart php
```

### 安PHP 扩展

首先要更新pecl协议： sudo pecl channel-update [pecl.php.net](http://pecl.php.net)

我建议尽可能使用pecl来安装C扩展。PECL安装的扩展不用配置，php -i查看发现直接生效。

如果要Web生效，重启php-fpm即可。

```
sudo pecl install redis

brew install libmemcached
brew install zlib
brew install pkg-config
sudo pecl install memcached
sudo pecl install channel://pecl.php.net/mcrypt-1.0.2
```

### 关于tuniu的php环境：

Version： php5.5.29

Extensions（php7.3.7未默认安装的）

- apc
- apcu
- libevent
- memcache
- xhprof
- mcrypt
- memcached
- redis
- magickwand
- imagick

注：

1. libevent、memcache在php7中已经不被支持，应用 event和memcached替代。
2. apc/apcu 一般也不需要了，自带zend opcache足够了 3