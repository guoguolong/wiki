macOS下1024端口以下的服务必须用sudo

比如我安装了nginx：

```
brew install nginx
```

然后启动81端口的nginx服务

```
sudo brew services start nginx
```

工作的很好，我把端口修改为80，死活启动不了。

打开“系统偏好设置”，防火墙也没开啊，但是最终推测是防火墙问题。macOS下防火墙软件叫pfctl(类似Linux下的iptables)，修改 pfctl 的配置文件 /etc/pf.conf，在文件末尾追加一行：

```
pass in proto tcp from any to any port 80
```

然后刷新 pcftl 的规则

```
sudo pfctl -F all
```