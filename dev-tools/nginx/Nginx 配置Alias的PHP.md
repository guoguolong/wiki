Alias支持PHP，要点是：

```nginx
fastcgi_param SCRIPT_FILENAME $request_filename;
```

完整配置：

```nginx
server {
    listen      80;
    server_name  web.kaulware.me;
    location /api {
        alias /projects/api/;
        location ~ \.php$ {
            fastcgi_index /index.php;
            fastcgi_pass 127.0.0.1:9000;
            include fastcgi_params;
            fastcgi_param SCRIPT_FILENAME $request_filename;
        }
    }
}
```