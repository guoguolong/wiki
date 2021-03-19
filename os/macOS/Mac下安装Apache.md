## I. 安装Apache 2.4

因为apache和php不在默认的仓库里，所以我们要先添加其所在的仓库。

brew tap homebrew/apache 然后 brew install httpd24 安装后提示：

> To have launchd start homebrew/apache/httpd24 now and restart at login: brew services start homebrew/apache/httpd24

Or, if you don't want/need a background service you can just run:

apachectl start

## II. 连接PHP

由于php7之前已经安装过了，那么一般和apache的连接模块应该都是安装好的，你只需要手工配置一下连接，修改httpd.conf文件内容如下：

```
LoadModule php7_module /usr/local/Cellar/php70/7.0.9/libexec/apache2/libphp7.so

<IfModule mod_php7.c>
    AddType application/x-httpd-php .php
    AddType application/x-httpd-php-source .phps
    <IfModule mod_dir.c>
        DirectoryIndex index.html index.php
    </IfModule>
</IfModule>
```

重启即可！