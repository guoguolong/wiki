很多情况下，我们需要一个共享的存储空间，用来存储数据。基于软件的支持性调研结果，WebDav应该是最为适合的一种。

### 1、首先要安装Nginx

```shell
apt install nginx-full
```

### 2、配置域名和目录（下为配置文件示例）

```nginx
server {
    listen 443 ssl http2;
    server_name test.com;

    ssl on;
    ssl_certificate /cert/test_ssl.pem;
    ssl_certificate_key /cert/test_ssl.key;
    root /webdata/test.com;
    if ( -d $request_filename ) {
      rewrite ^(.*[^/])$ $1/ break;
    }
    location / {
      charset         utf-8;
      autoindex       on;

      client_body_temp_path   /etc/nginx/client_temp;
      client_max_body_size    0;

      dav_methods PUT DELETE MKCOL COPY MOVE;
      dav_ext_methods PROPFIND OPTIONS;

      create_full_put_path    on;
      dav_access              group:rw all:r;

      auth_basic              "Access limited";
      auth_basic_user_file    /etc/nginx/user.passwd;
    }
}
```

### 3、创建鉴权文件：/etc/nginx/user.passwd
### 4、设置账户密码（下为示例）

```
echo "用户名:$(openssl passwd 密码)" >/etc/nginx/user.passwd
```

### 5、重启Nginx即可。

### 6、客户端工具

Mac 的 Finder 就可以做为 WebDav 客户端工具使用

**附录** · WebDav 其他连接工具

* Mac：（APP Store）推荐FE File Explorer
* iPhone：（APP Store）推荐FE File Explorer
* Windows：推荐RaiDrive（官网下载地址：https://www.raidrive.com/download）