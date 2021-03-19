## 1. 安装php7.1

```
brew install php71
```

## 2. 安装pear和pecl

pear包包含了pecl，执行：

> curl -o go-pear.php http://pear.php.net/go-pear.phar

php go-pear.php

根据提示操作，即可完成安装. 然后将pear命令的路径加入到~/.bash_profile

## 3. 安装php7.1的c扩展

### 1) brew 方式

```
brew install php71
brew install php71-yaf
brew install php71-mcrypt
brew install php71-swoole
brew install php70-imagick
brew install php71-msgpack
brew install php71-phalcon
brew install php71-yaml
brew install php71-redis
brew install php71-memcached
brew install php71-intl
brew install php71-igbinary
brew install php71-event
```

### 2) pecl方式

- Yar

  pecl安装yar报错 configure: error: Please reinstall the libcurl distribution - easy.h should be in <curl-dir>/include/curl/，解决办法：

  > brew install curl

  xcode-select --install 然后执行

  > sudo pecl install channel://pecl.php.net/yar-2.0.1

### 3) 源码安装方式

- libevent

> wget https://github.com/expressif/pecl-event-libevent/archive/master.zip

phpize && ./configure && make && make install

event是libevent的类移植版本，php7下推荐event。

## 4. 运行php-fpm(与nginx连接)

前面brew安装php7.1就会在php7.1的sbin目录下同时安装好了 php-fpm

```
php-fpm &
```

停止貌似只能kill

## 5. 安装composer

请参考：https://getcomposer.org/download/