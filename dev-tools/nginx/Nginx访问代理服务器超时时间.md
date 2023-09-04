如果GET到代理服务器的请求一直没返回，使用proxy_read_timeout来设置超时时间是最合理的

proxy_read_timeout 该指令设置与代理服务器的读超时时间。它决定了nginx会等待多长时间来获得请求的响应。这个时间不是获得整个response的时间，而是两次reading操作的时间。

```nginx
server {
    listen      80;
    server_name  ares.tuniu.com;
    location / {
        proxy_set_header Host $host;
        proxy_read_timeout        4; #8s后超时
        proxy_pass http://localhost:4002;
        proxy_redirect default;
    }
    error_page  500 502 503 504  /50x.html;
    location = /50x.html {
        root /projects/portal;
    }
}
```