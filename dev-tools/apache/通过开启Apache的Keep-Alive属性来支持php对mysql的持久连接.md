php对mysql的持久连接函数为：mysql_pconnect()

/etc/httpd/conf/httpd.conf

```
# 需要在Apache的配置文件设置几个参数
# KeepAlive: Whether or not to allow persistent connections (more than one request per connection). Set to "Off" to deactivate.
Keep-Alive on
# MaxKeepAliveRequests: The maximum number of requests to allow during a persistent connection. Set to 0 to allow an unlimited amount. We recommend you leave this number high, for maximum performance.       
MaxKeepAliveRequests 0
# KeepAliveTimeout: Number of seconds to wait for the next request from the same client on the same connection.
KeepAliveTimeout 15 # 
```

客户端发送HTTP请求成功之后，Apache将不会立刻断开socket，而是一直监听客户端这一请求，持续时间为15秒，如果超过这一时间，Apache就立即断开socket。 注意：Apache的版本为2.0以上。