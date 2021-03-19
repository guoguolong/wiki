## 简介

[Let's Encrypt](https://links.jianshu.com/go?to=https%3A%2F%2Fletsencrypt.org%2F) —— 是一个由非营利性组织 [互联网安全研究小组](https://links.jianshu.com/go?to=https%3A%2F%2Fletsencrypt.org%2Fisrg%2F)（ISRG）提供的免费、自动化和开放的证书颁发机构（CA），简单的说，就是为网站提供免费的 SSL/TLS 证书。

## 签发工具

Let's Encrypt 生成证书的[工具](https://links.jianshu.com/go?to=https%3A%2F%2Fletsencrypt.org%2Fdocs%2Fclient-options%2F)很多，[certbot](https://links.jianshu.com/go?to=https%3A%2F%2Fcertbot.eff.org%2F) 是官方推荐的签发工具，也可以通过在线服务申请，例如：

- [FreeSSL](https://links.jianshu.com/go?to=https%3A%2F%2Ffreessl.cn%2F)（不支持自动续签）
- [七牛](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.qiniu.com%2Fssl)（不支持 DV 泛域名）
- [又拍云](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.upyun.com%2Fproducts%2Fssl)（不支持 DV 泛域名）

在线一站式服务管理方便，但可能需要绑定业务，个人用户还是推荐通过工具生成证书。
 这里推荐 [acme.sh](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2FNeilpang%2Facme.sh)，它不仅有详细的[中文文档](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2FNeilpang%2Facme.sh%2Fwiki%2F%E8%AF%B4%E6%98%8E)，操作更为方便，还支持 [Docker](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2FNeilpang%2Facme.sh%2Fwiki%2FRun-acme.sh-in-docker)。

## acme.sh

ssh 登录域名所在的 nginx 服务器，切换到 root 身份

### 1. 下载安装

```sh
curl https://get.acme.sh | sh
```

## 2. 证书申请

通过 DNS 生成证书配置，我用的是Aliyun，首先去aliyun.com获得 API Key 和 API Secret：

```sh
export Ali_Key="LTAI4Fs6tL4smPA5vLbY7CSM"
export Ali_Secret="274WVOXOjVDl7gwV80K86Xb7ehA1M4"

acme.sh --issue --dns dns_ali -d shunyii.cn -d *.shunyii.cn
```

### 3. Nginx 配置



```sh
server {
    listen 80;
    server_name shunyii.cn *.shunyii.cn;
    # 重定向到 https
    rewrite ^(.*)$  https://$host$1 permanent;
}

server {
    listen 443;
    server_name shunyii.cn *.shunyii.cn;
    root   /websvr/www/shunyii.cn;
    index  index.html index.htm;

    access_log /dev/null;
    error_log  /websvr/log/nginx/shunyii.cn.error.log warn;

    # SSL 配置
    ssl on;
    ssl_certificate /websvr/ssl/fullchain.cer; # 证书文件
    ssl_certificate_key /websvr/ssl/shunyii.cn.key; # 私钥文件
    ssl_session_timeout 5m; # 会话缓存过期时间
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2; # 开启 SSL 支持
    ssl_prefer_server_ciphers on; # 设置协商加密算法时，优先使用服务端的加密套件
}
```

重启 Nginx 服务：

```sh
nginx -s reload
```

### SSL 检测

反问 [https://myssl.com](https://links.jianshu.com/go?to=https%3A%2F%2Fmyssl.com)

### 更新证书

定时任务会自动更新

强制续签证书：`acme.sh --renew -d mydomain.com --force`