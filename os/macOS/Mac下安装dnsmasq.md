DNS泛解析替代/etc/hosts方便多了，dnsmasq就是这么一个小巧的工具。

## 1. 安装dnsmasq

> brew install dnsmasq

安装成功后提示

> cp /usr/local/opt/dnsmasq/dnsmasq.conf.example /usr/local/etc/dnsmasq.conf

> sudo brew services start dnsmasq

## 2. 配置dnsmasq

### 1.1. 配置文件/usr/local/etc/dnsmasq.conf

根据上述成功安装提示照做，生成/usr/local/etc/dnsmasq.conf，编辑内容如下：

```
resolv-file=/etc/resolv.conf   # 配置上行DNS，对应no-resolv

strict-order # resolv.conf内的DNS寻址严格按照从上到下顺序执行，直到成功为止

addn-hosts=/etc/hosts   # DNS解析hosts时对应的hosts文件，对应no-hosts

cache-size=1024 

listen-address=192.168.x.x,127.0.0.1  #多个ip用逗号分隔，192.168.x.x表示本机的ip地址，只有127.0.0.1的时候表示只有本机可以访问。通过这个设置就可以实现同一局域网内的设备，通过把网络DNS设置为本机ip从而实现局域网范围内的DNS泛解析(注：无效ip有可能导至服务无法启动）



address=/kaluware.me/127.0.0.1 # 重要！！这一行就是你想要泛解析的域名配置.
```

以上几乎是最简配置.

reolve-file=/etc/resolv.conf配置上行DNS，假设/etc/resolv.conf内容如下：

```
nameserver 183.44.22.19
```

那就是说如果你访问域名abc.com没有被dnsmasq解析，它会尝试访问 183.44.22.19去解析。

### 1.2. 客户端指定域名服务器/etc/resolv.conf

你的mac可能同时就是你的dns使用者，所有，需要：系统偏好配置->网络->(你的连接)->DNS增加了一个条目：

> nameserver 127.0.0.1 #局域网其它机器则换成实际dnsmasq的IP地址。

一般这一行放到最上面，会优先DNS解析

## 3. 启动dnsmasq

> sudo brew services start dnsmasq

如果改动了泛解析规则，重启dnsmasq不会立即看到效果，因为有缓存，可以稍等便可或清除一下缓存再试

> sudo killall -HUP mDNSResponder