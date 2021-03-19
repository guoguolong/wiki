Ubuntu用Apache搭建Git服务器 ，可以通过web提交／更新代码！

## I. Git的安装与配置

### 1. 安装git

> yum install git

### 2. 安装 git-core（为了使用git-http-backend——支持git的CGI程序，apache支持git就靠它）

> yum install git-core

### 3. 创建存放git repository的文件夹，并

### 4. 创建一个空的项目

```
cd /home && sudo mkdir git
sudo mkdir git-test
cd git-test
sudo git init —bare
sudo chown -R ww-data:www-data .
```

## II. Apache的配置

### 1. 创建用于git用户验证的帐户（用户帐户由apache管理）

```
sudo htpasswd -m -c /etc/apache2/sites/git-team.htpasswd <username>
sudo chown www-data:www-data /etc/apache2/sites/git-team.htpasswd
sudo chmod 640 /etc/apache2/sites/git-team.htpasswd
```

### 2. 修改apache配置文件httpd.conf

```
<VirtualHost *:80>
    ServerName git.babyun.cn
    SetEnv GIT_HTTP_EXPORT_ALL
    SetEnv GIT_PROJECT_ROOT /home/git
    ScriptAlias /repo/ /usr/lib/git-core/git-http-backend/
    DocumentRoot /home/git
    <Location />
        Options +ExecCgi -MultiViews +SymLinksIfOwnerMatch
        Order allow,deny
        Allow from all
        AuthType Basic
        AuthName "Zeus Git"
        AuthUserFile /etc/apache2/sites/git-team.htpasswd
        Require valid-user
    </Location>
</VirtualHost>
```

重启以前如果cgi还未允许，可能需要执行sudo a2enmod cgi, 然后重启：3. 重启apache使设置生效 service httpd restart

## III. 客户端访问Git服务器

运行以下命令签出git-test项目：

> git clone http://git.babyun.cn/repo/git-test

输入用户名与密码，如果输出下面的信息，就说明签出成功。

```
remote: Counting objects: 6, done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 6 (delta 0), reused 0 (delta 0)
Unpacking objects: 100% (6/6), done.
```