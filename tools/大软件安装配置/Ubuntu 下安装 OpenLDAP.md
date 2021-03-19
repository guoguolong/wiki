参考文章：https://www.linuxidc.com/Linux/2017-05/143762.htm

# 下载

```
sudo apt-get install -y slapd ldap-utils
```

# 配置

配置文件在/etc/ldap/ldap.conf，添加BASE 和 URI:

```
BASE dc=ban,dc=com
URI ldap://192.168.5.180:389
```

如果默认配置不满足需求，通过如下命令，对slapd进行再配置：

```
dpkg-reconfigure slapd
```

注：reconfigure的内容，包括baseDN，admin管理员密码，后端数据库选择(HDB,BDB)，是否删除旧的数据库，是否允许LDAPv2协议

# 安装php的ldap管理端软件：

```
apt-get install -y phpldapadmin
```

修改相应的配置文件/etc/phpldapadmin/config.php，做如下修改：

```
$servers = new Datastore();
$servers->newServer('ldap_pla');
$servers->setValue('server','name','My LDAP Server');
$servers->setValue('server','host','127.0.0.1');
$servers->setValue('server','port',389);
$servers->setValue('server','base',array('dc=banyuan,dc=club')); #修改 dc=banyuan,dc=club 为自己的域名
$servers->setValue('login','auth_type','session');
$servers->setValue('login','bind_id','cn=root,dc=banyuan,dc=club'); #修改 dc=banyuan,dc=club 为自己的域名
$servers->setValue('login','bind_pass','Banyuan2019'); #填入 5.7.1 设定的根节点管理员密码
$servers->setValue('server','tls',false); 
```

配置nginx，使ldap.banyuan.club指向phpLDAPadmin

- phpLDAPadmin登录时候，有错提示，patch（进入目录/usr/share/phpldapadmin/先）： vim htdocs/index.php

```
ini_set('display_errors',1); 
# 修改为
ini_set('display_errors',0);
```

vim lib/common.php

```
error_reporting(E_ALL);
# 修改为：
error_reporting(E_ALL & ~E_DEPERCATED);
```

- openLDAP安装时无法操作根节点数据，提示的是This base cannot be created with PLA. 参考： http://www.cnblogs.com/xiaomifeng0510/p/9564653.html
- 下载JDK时，如何通过协议！！ wget --no-check-certificate --no-cookies --header "Cookie: oraclelicense=accept-securebackup-cookie"

### [ldap.silencex.com](http://ldap.silencex.com)

用户名：cn=admin,dc=banyuan,dc=club 密码：Banyuan2019