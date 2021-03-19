1. apache 启动 gzip

1)、修改Apache的http.conf文件，去除mod_deflate.so前面的注释

```
LoadModule deflate_module modules/mod_deflate.so
```

2)、修改Apache的http.conf文件

```
#GZIP压缩模块配置
<ifmodule mod_deflate.c>
#启用对特定MIME类型内容的压缩
SetOutputFilter DEFLATE
SetEnvIfNoCase Request_URI .(?:gif|jpe?g|png|exe|t?gz|zip|bz2|sit|rar|pdf|mov|avi|mp3|mp4|rm)$ no-gzip dont-vary #设置不对压缩的文件
AddOutputFilterByType DEFLATE text/html text/css text/plain text/xml application/x-httpd-php application/x-javascript application/json #设置对压缩的文件
</ifmodule>
```

1. apache keep-alive 修改Apache的http.conf文件，添加

```
KeepAlive on
MaxKeepAliveRequests 0
KeepAliveTimeout 15
```

1. apache 限速 安装mod_bw.so

```
BandWidthModule On
ForceBandWidthModule On
#限制为500K
BandWidth all 102400
#限制为100个连接数
MaxConnection all 100
```