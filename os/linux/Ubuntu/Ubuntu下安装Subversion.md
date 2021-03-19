## 1. 软件安装

若系统尚未安装 Apache ，需要先安装 Apache ，最好是 Apache2

> $ sudo apt-get install apache2

之后安装 Subversion 以及 Apache2 模块

> $ sudo apt-get install subversion libapache2-svn

如果不出意外，svn相应的apache模块都已经安装好了。 注：安装过程中遇到问题，一定要反复apt-get --purge操作，让环境比较干净后再重新上述安装步骤.

## 2. 创建用户组

```
$ groupadd subversion
$ usermod -G subversion www-data   ( 将apache2的执行www-data用户加入该组 )
```

## 3. 新建版本库

```
$ mkdir /home/svn
$ chown -R www-data:subversion /home/svn
$ chmod -R g+rs /home/svn
$ svnadmin create /home/svn/myproject   ( 建立仓库 )
$ chmod -R g+rw myproject
```

myproject目录应让www-data有完整的权限：

> chown -R www-data:subversion fish

接下来是和 Apache 结合的 Subversion 配置步骤。

## 4. 添加虚拟目录

```
# SVN Repository
<Location /svn>
DAV svn
SVNParentPath /home/svn
SVNListParentPath on 
AuthType Basic
AuthName "My SVN Server"
AuthUserFile /etc/apache2/svn.passwd
AuthzSVNAccessFile /etc/apache2/svn.access
<LimitExcept GET PROPFIND OPTIONS REPORT>
   Require valid-user
</LimitExcept>
</Location>
```

svn.passwd,svn.access文件要自己生成，

1). 如何生成svn.passwd文件：

htpasswd –c /etc/apache2/svn.passwd <your username>

接下来创建新用户的密码就不要用-c参数了

2). svn.access生成规则(如果需要的话)

```
[general]
anon-access = none
auth-access = write

[groups]
admin = root,allen
devteam1 = allen,root,kefe

[/]
* = r
@admin = rw
@devteam1 = rw
```

## 5. 重启动 Apache2

> $ service apache2 restart

可以测试了

## 6. 更好的虚拟机配置

```
<VirtualHost *:81>
    ServerName svn.kaulware.com
    DocumentRoot /
    <Location />
        DAV svn
        SVNParentPath /home/svnroot/repo
        SVNListParentPath on
        AuthType Basic
        AuthName "MirrorOffice SVN Server"
        AuthUserFile /etc/apache2/svn.passwd
        Require valid-user
    </Location>
    <Directory /home/svnroot/repo>
        Order deny,allow
        Allow from all
        Require all granted
    </Directory>
</VirtualHost>
```