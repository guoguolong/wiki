# I.下载

官网下载tar.gz（绿色版）

# II. 设置和安装

解压缩后，进入解压缩目录，假设解压缩到/server/bitbucket。

### 设置JAVA_HOME

vim ./bin/set-jre-home.sh 第一行设置JAVA_HOME，例如我的是JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64 Ubuntu下如果apt安装的openjdk，通过ls -l查看java命令的软连接最终找到JAVA_HOME Mac下JAVA_HOME在哪里? 这边文章写得好：https://blog.csdn.net/a158123/article/details/79684499

### 设置 BITBUCKET_HOME

vim ./bin/set-jre-home.sh 第一行设置BITBUCKET_HOME，我这里 BITBUCKET_HOME= /projects/bitbucket

**注：BITBUCKET_HOME的目录一定不能和解压缩目录(/server/bitbucket)相同。**

# III. 破解

参照【附件】目录下的Bitbucket5.x_Cracker.zip 破解（复制jar包到WEB-INF/lib目录）

# III. 运行服务器

执行 ./bin/start-bitbucket.sh，将启动web server和ElasticSearch

# IV. 浏览器访问

http://localhost:7990 进行安装，安装时尽管要去atlassian获取evaluation license，但是安装好之后，其实已经破解了。通过后台'licenses'菜单可以查看。

# 配置Nginx SSL

Nginx SSL配置（一个例子）如下并重启

```
upstream git.banyuan.club {
    server localhost:7990;
}

server {
    listen 80;
    server_name git.banyuan.club;
    root /projects/bitbucket;
    index index.html;
    rewrite ^(.*) https://$server_name$1 permanent;
}

server {
    server_name git.banyuan.club;
    listen 443;
    ssl on;
    ssl_certificate /etc/nginx/cert/banyuan.club.pem;
    ssl_certificate_key /etc/nginx/cert/banyuan.club.key;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;

    index index.html;

    location / {
        proxy_read_timeout 900;
        proxy_pass http://git.banyuan.club;
        proxy_set_header X-Forwarded-Host $host;
        proxy_set_header X-Forwarded-Server $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_redirect off;
    }
}
```

在Bitbucket端，

- 后台修改baseUrl
- BITBUCKET_HOME所在目录的share/bitbucket.properties，添加：

```
server.port=7990
server.secure=true
server.scheme=https
server.proxy-port=443
server.proxy-name=git.banyuan.club
```

重启Bitbucket 更多参考 https://confluence.atlassian.com/bitbucketserver/securing-bitbucket-server-behind-nginx-using-ssl-776640112.html

# V. LDAP设置

ldap设置好了，只能在ldap服务器添加账户和密码，在bitbucket里读取，密码都不可以修改。

怎么操作LDAP？装好OpenLDAP，通过phpLDAPadmin系统创建用户， 节点类型： Kolab: User Entry objectClass：inetOrgPerson