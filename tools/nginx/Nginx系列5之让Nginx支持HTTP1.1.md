Nginx在反向代理HTTP协议的时候，默认使用的是HTTP1.0去向后端服务器获取响应的内容后在返回给客户端。 HTTP1.0和HTTP1.1的一个不同之处就是，HTTP1.0不支持HTTP keep-alive。nginx在后端服务器请求时使用了HTTP1.0同时使用HTTP Header的Connection：Close通知后端服务器主动关闭连接。这样会导致任何一个客户端的请求都在后端服务器上产生了一个TIME-WAIT状态的连接。所以我们需要在Nginx上启用HTTP1.1的向后端发送请求，同时支持Keep-alive。

```nginx
http{
    # 省去其他的配置
    upstream www{
        keepalive 50; # 必须配置，建议50-100之间
    }
    server {
        # 省去其他的配置
        location / {
            proxy_http_version 1.1; # 后端配置支持HTTP1.1，必须配
            proxy_set_header Connection "";   # 后端配置支持HTTP1.1 ,必须配置。
        }
    }
}
```