SSH服务是目前代替telnet的最安全有效的方法。目前大多数ssh服务是运行在 Linux系统上的sshd服务。当访问终端在windows上时，各终端软件，如，putty，SecureCRT等，大多支持设置向服务器端自动发送 消息，来防止终端定期超时。其实，服务器端也支持类似的设置，从服务器的角度防止链接超时。并且，当终端在Ubuntu 等Linux系统上时，客户端也可进行类似设置。 下面我们就介绍，后两种情况的设置方法以及通过设置shell变量来达到此目的的方法：

## 1、 配置服务器

$vi /etc/ssh/sshd_config

修改两项参数后如下： ClientAliveInterval 60 ClientAliveCountMax 3 ### 0 不允许超时次数

将 ClientAliveInterval 0和ClientAliveCountMax 3的注释符号去掉,将ClientAliveInterval对应的0改成60，没有就自己输入。

1. ClientAliveInterval 指定了服务器端向客户端请求消息 的时间间隔, 默认是0, 不发送.而ClientAliveInterval 60表示60s(每分钟)发送一次, 然后客户端响应, 这样就保持长连接了.
2. ClientAliveCountMax, 使用默认值3即可. ClientAliveCountMax表示服务器发出请求后客户端没有响应的次数达到一定值, 就自动断开. 正常情况下, 客户端不会不响应. 重新加载sshd服务。退出客户端，再次登陆即可验证。

## 2、 配置客户端

> $vi /etc/ssh/ssh_config
> ServerAliveInterval 300