### 1. 下载安装

Gitlab包有270m，下载起来极其困难，于是从朋友那里下载了gitlab-ce_8.5.0-ce.1_amd64.deb， wget https://packages.gitlab.com/gitlab/gitlab-ce/packages/ubuntu/trusty/gitlab-ce_8.5.0-ce.1_amd64.deb/download 然后执行：sudo dpkg -i gitlab-ce_8.5.0-ce.1_amd64.deb 注：可能返回Errno::ENOMEM: Cannot allocate memory - gzip，原来是我1核2g的内存配置太小，于是安装swap，执行成功

可以通过 sudo gitlab-ctl restart命令重新启动 gitlab服务.

### 2. gitlab 配置

```
sudo vim /etc/gitlab/gitlab.rb

# 配置 nginx 访问
external_url 'http://gitlab.kaulware.com'
nginx['enable'] = false
web_server['external_users'] = ['www-data']


# 配置发送邮件
gitlab_rails['smtp_enable'] = true
gitlab_rails['smtp_address'] = "smtp.exmail.qq.com"
gitlab_rails['smtp_port'] = 465
gitlab_rails['smtp_user_name'] = "oa@babyun.cn"
gitlab_rails['smtp_password'] = "babyun2014"
gitlab_rails['smtp_domain'] = "smtp.exmail.qq.com"
gitlab_rails['smtp_authentication'] = "login"
gitlab_rails['smtp_enable_starttls_auto'] = true
gitlab_rails['smtp_openssl_verify_mode'] = 'none'

# If your SMTP server does not like the default 'From: gitlab@localhost' you
# can change the 'From' with this setting.
gitlab_rails['gitlab_email_from'] = 'oa@babyun.cn'
gitlab_rails['gitlab_email_reply_to'] = 'noa@babyun.cn'

sudo gitlab-ctl reconfigure
```

### nginx 配置

```
# gitlab socket 文件地址
upstream gitlab { 
server unix://var/opt/gitlab/gitlab-workhorse/socket fail_timeout=0;
}

server {
  listen *:80;
  server_name gitlab2.babyun.cn;   # 请修改为你的域名
  server_tokens off;     # don't show the version number, a security best practice
  root /opt/gitlab/embedded/service/gitlab-rails/public;
  # Increase this if you want to upload large attachments
  # Or if you want to accept large git objects over http
  client_max_body_size 250m;
  # individual nginx logs for this gitlab vhost
  access_log  /var/log/gitlab/nginx/gitlab_access.log;
  error_log   /var/log/gitlab/nginx/gitlab_error.log;

  location / {
    # serve static files from defined root folder;.
    # @gitlab is a named location for the upstream fallback, see below
    try_files $uri $uri/index.html $uri.html @gitlab;
  }
  # if a file, which is not found in the root folder is requested,
  # then the proxy pass the request to the upsteam (gitlab unicorn)
  location @gitlab {
    # If you use https make sure you disable gzip compression 
    # to be safe against BREACH attack
    proxy_read_timeout 300; # Some requests take more than 30 seconds.
    proxy_connect_timeout 300; # Some requests take more than 30 seconds.
    proxy_redirect     off;
    proxy_set_header   X-Forwarded-Proto $scheme;
    proxy_set_header   Host              $http_host;
    proxy_set_header   X-Real-IP         $remote_addr;
    proxy_set_header   X-Forwarded-For   $proxy_add_x_forwarded_for;
    proxy_set_header   X-Frame-Options   SAMEORIGIN;
    proxy_pass http://gitlab;
  }
  # Enable gzip compression as per rails guide: http://guides.rubyonrails.org/asset_pipeline.html#gzip-compression
  # WARNING: If you are using relative urls do remove the block below
  # See config/application.rb under "Relative url support" for the list of
  # other files that need to be changed for relative url support
  location ~ ^/(assets)/  {
    root /opt/gitlab/embedded/service/gitlab-rails/public;
    # gzip_static on; # to serve pre-gzipped version
    expires max;
    add_header Cache-Control public;
  }
  error_page 502 /502.html;
}
```

### 默认账号

```
root/[default]
```

### 3. 免登录push/pull远程仓库.

#### 1). git://协议

ssh-keygen -t rsa生成公钥私钥对文件：id_rsa, id_rsa.pub，将id_rsa.pub里面的文本复制到gitlab的SSH Keys，就可以了

#### 2). [http://协议](http://xn--ykru52k)

http协议只读仓库，无法写入，免登录方法需要在本地家目录下创建.netrc文件，内容如下：

```
machine gitlab.kaulware.com
login allen
password justatestp
```

重要提示：如果访问gitlab返回502错误：Whoops, GitLab is taking too much time to respond. 我发现重启：sudo gitlab-ctl -restart 或者 重启nginx就好了