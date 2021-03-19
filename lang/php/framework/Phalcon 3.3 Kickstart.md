## 下载安装phalcon

https://phalconphp.com/zh/download/linux

> git clone --depth=1 "git://github.com/phalcon/cphalcon.git" cd cphalcon/build sudo ./install

## 下载安装phalcon开发工具

进入文档 https://docs.phalconphp.com/en/3.3/devtools-installation 节，有详细介绍：

> git clone git://github.com/phalcon/phalcon-devtools.git ln -s your_dir/phalcon-devtools/phalcon.php /usr/local/bin/phalcon chmod ugo+x /usr/local/bin/phalcon

现在你就可以在命令行下运行phalcon了

## 创建项目手脚架

> phalcon project rocket

就创建一个名字为 rocket的Web项目

## Web访问 - php内置服务器

可以用php内置服务器进行调试，在项目根目录下执行

> php -S localhost:9000 -t public/ .htrouter.php

然后访问http://localhost:9000就可以看到Congratulation页面了

## Web访问 - 配置Nginx代理访问PHP

https://docs.phalconphp.com/en/latest/webserver-setup#nginx

```nginx
    server {
        listen      80;
        server_name localhost.dev;

        # This is the folder that index.php is in
        root /var/www/phalcon/public;
        index index.php index.html index.htm;

        charset utf-8;

        location / {
            try_files $uri $uri/ /index.php?_url=$uri&$args;
        }

        location ~ \.php {
            fastcgi_pass 127.0.0.1:9000;
            fastcgi_index /index.php;

            include fastcgi_params;
            fastcgi_split_path_info       ^(.+\.php)(/.+)$;
            fastcgi_param PATH_INFO       $fastcgi_path_info;
            fastcgi_param PATH_TRANSLATED $document_root$fastcgi_path_info;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        }

        location ~ /\.ht {
            deny all;
        }
    }
```

再来一个虚拟目录（Alias）的配置例：

```nginx
location /ark {
        index index.php index.html index.htm;
        alias /projects/ark/public/;
        rewrite ^/ark/?(.+)$ /ark/index.php?_url=/$1 last;

        location ~ \.php$ {
            fastcgi_index /index.php;
            fastcgi_pass 127.0.0.1:9000;
            # fastcgi_pass unix:/run/php/php7.1-fpm.sock;
            # include snippets/fastcgi-php.conf;
            include fastcgi_params;
            fastcgi_param SCRIPT_FILENAME $request_filename;
        }
}
```

alias方式不能用try_files，它会尝试寻找server块root目录下的文件.

## Composer应用

## 命令行应用