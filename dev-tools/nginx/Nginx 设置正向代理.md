Nginx配置例子：

```nginx
server {
    # resolver 8.8.8.8;
    listen       9999;
    server_name  achilles.tuniu.com;

    proxy_set_header  Host             $host;  
    proxy_set_header  X-Forwarded-For  $proxy_add_x_forwarded_for; 
    location / {
        proxy_pass http://localhost:4006/;
    }
```

关键：

1. listen端口： 不能有多个虚拟主机都监听 9999
2. server_name是可选的
3. resolver 是可选的