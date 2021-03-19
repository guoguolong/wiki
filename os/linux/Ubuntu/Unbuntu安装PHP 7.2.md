我使用ppa安装php7.2

```
add-apt-repository ppa:ondrej/php 
# 可能提示你php 5.5, php5.6, php7.0，意味这些包都出现在仓库里，需要你选择安装具体的一个
apt-get update
```

## 1.安装php7.2核心

```
sudo apt-get install php7.2
sudo apt-get install php7.2-dev # 否则phpize怎么用？
```

## 2. apt-get安装PHP扩展

```
#基本常用
sudo apt install php7.2-bcmath
sudo apt install php7.2-intl
sudo apt install php7.2-mysql
sudo apt install php7.2-mbstring
# sudo apt install php7.2-mcrypt 找不到
sudo apt install php7.2-gd
sudo apt install php7.2-imagick
sudo apt install php7.2-redis
sudo apt install php7.2-soap
sudo apt install php7.2-zip

curl -s "https://packagecloud.io/install/repositories/phalcon/stable/script.deb.sh" | sudo bash
sudo apt-get install php7.1-phalcon
```

## 3. pecl安装PHP扩展

\--

```
sudo pecl install swoole
sudo pecl install msgpack
sudo pecl install yaf
sudo pecl install yar  #可能的依赖安装：apt-get install curl libcurl4-gnutls-dev
sudo pecl install yaml  #可能的依赖安装：apt-get install libyaml-dev
sudo pecl install memcached  #可能的依赖安装：apt-get install pkg-config zlib1g-dev libevent-dev libmemcached-dev
sudo pecl install proctitle
```

## 4. 源码安装PHP扩展

```
# libevent
wget https://github.com/expressif/pecl-event-libevent/archive/master.zip 
phpize && ./configure && make && make install

# xhprof
pecl install channel://pecl.php.net/xhprof-0.9.4 #不能安装
只能通过源码安装 https://github.com/longxinH/xhprof
```

apt-get安装的PHP扩展自动设置php.ini关联so文件 , 但是对于pecl和源码安装的扩展需要手动设置php.ini关联。比如imagick扩展要在php-fpm和cli中生效，就需要在/etc/php/7.1/mods-available/ 目录添加文件imagick.ini内容如下：

```
extension=imagick.so
```

然后在/etc/php/7.1/cli/conf.d 和 /etc/php/7.1/php-fpm/conf.d下设置软连接该

```
sudo ln -s ../../mods-available/imagick.ini 30-imagick.ini
```

## 5. 与Nginx连接

```
sudo apt-get install php7.2-fpm
sudo service php7.2-fpm restart
```

注意默认/etc下php-fpm.conf包含pool.d/www.conf，将listen = /run/php/php7.2-fpm.sock 应修改为：listen = 127.0.0.1:9000，便于nginx连接（个人习惯，不是必须）

## 6. 安装composer

```
sudo apt install composer 
```

或者源码安装： https://getcomposer.org/download/