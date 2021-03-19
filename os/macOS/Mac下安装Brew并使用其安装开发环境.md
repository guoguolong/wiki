## I. 首先要安装Brew http://brew.sh/

执行命令 :

> ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

## II. 要安装的开发环境

- brew install node

  > 默认安装了6.3.1，但是我需要4的lts版本，所以这里涉及到多版本brew软件安装问题

  ```
  brew unlink node
  
    brew install homebrew/versions/node4-lts
  
    执行node命令，当前环境就变成4的版本了。
  
    执行命令 ls /usr/local/Cellar/node* 发现有俩版本node安装好了
  
  
  
    /usr/local/Cellar/node:
  
    6.3.1
  
    /usr/local/Cellar/node4-lts:
  
    4.4.7
  ```

  > 这时如果想在两个版本环境中切换可以执行：

  ```
  // 切换到6.3.1
  
    brew unlink node4-lts
  
    brew switch node 6.3.1
  
  
  
    // 切换到4.4.7
  
    brew unlink node
  
    brew switch node4-lts 4.4.7
  ```

- brew install mysql

  > mysql.server start

- brew install redis

  > To have launchd start redis now and restart at login:

  ```
  brew services start redis
  ```

  Or, if you don't want/need a background service you can just run:

  ```
  redis-server /usr/local/etc/redis.conf
  ```

- brew install nginx

  ```
  >brew services start nginx
  
    Or, if you don't want/need a background service you can just run:
  
          nginx
  ```

注：需要sudo执行： sudo brew services start nginx

有一次！！为了安装realip模块，不得不重新安装nginx: http://brew.sh/homebrew-nginx/ (安装之前先brew unlink nginx) 然后执行：

brew install nginx-full --with-realip

新的启动命令变成了： sudo brew services start homebrew/nginx/nginx-full

转发头的指令是：

```
proxy_set_header  Host             $host;  

proxy_set_header  X-Real-IP        $remote_addr;  

proxy_set_header  X-Forwarded-For  $proxy_add_x_forwarded_for; 

  # 不知为何以下两行不生效，但却应用在我们服务器上

  # real_ip_header X-Forwarded-For;

  # real_ip_recursive on;
```

- brew install memcached

  > To have launchd start memcached now and restart at login:

  ```
  brew services start memcached
  
  Or, if you don't want/need a background service you can just run:
  
      /usr/local/opt/memcached/bin/memcached
  ```

- brew install homebrew/php/php70

  > 启动php-fpm:

  ```
  brew services start homebrew/php/php70
  ```

## III. 让Sublime Text3使用指定目录的php

Tools->Build System->New Build System...

```
// php.sublimie-build内容为

{ 

     "cmd": ["/usr/local/bin/php", "$file"], "file_regex": "php$", "selector": "source.php"

}
```

注意：cmd值指示php具体路径。