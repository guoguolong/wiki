## 一、OpenLDAP简介

在安装OpenLDAP之前，我们首先来介绍下LDAP。

LDAP是一款轻量级目录访问协议（Lightweight Directory Access Protocol，简称LDAP），属于开源集中账号管理架构的实现，且支持众多系统版本，被广大互联网公司所采用。

LDAP提供并实现目录服务的信息服务，目录服务是一种特殊的数据库系统，对于数据的读取、浏览、搜索有很好的效果。目录服务一般用来包含基于属性的描述性信息并支持精细复杂的过滤功能，但OpenLDAP目录服务不支持通用数据库的大量更新操作所需要的复杂的事务管理或回滚策略等。

LDAP具有两个标准，分别是X.500和LDAP。OpenLDAP是基于X.500标准的，而且去除了X.500复杂的功能并且可以根据自我需求定制额外扩展功能，但与X.500也有不同之处，例如OpenLDAP支持TCP/IP协议等，目前TCP/IP是Internet上访问互联网的协议。

OpenLDAP可以直接运行在更简单和更通用的TCP/IP或其他可靠的传输协议层上，避免了在OSI会话层和表示层的开销，使连接的建立和包的处理更简单、更快，对于互联网和企业网应用更理想。

OpenLDAP目录中的信息是以树状的层次结构来存储数据（这很类同于DNS），最顶层即根部称作“基准DN”，形如“dc=mydomain,dc=org”或者“[o=mydomain.org](http://o=mydomain.org)”，前一种方式更为灵活也是Windows AD中使用的方式。在根目录的下面有很多的文件和目录，为了把这些大量的数据从逻辑上分开，OpenLDAP像其它的目录服务协议一样使用OU（Organization Unit，组织单元），可以用来表示公司内部机构，如部门等，也可以用来表示设备、人员等。同时OU还可以有子OU，用来表示更为细致的分类。

OpenLDAP中每一条记录都有一个唯一的区别于其它记录的名字DN（Distinguished Name）,其处在“叶子”位置的部分称作RDN(用户条目的相对标识名)。如dn:cn=tom,ou=animals,dc=ilanni,dc=com中cn即为RDN，而RDN在一个OU中必须是唯一的。

OpenLDAP默认以Berkeley DB作为后端数据库，BerkeleyDB数据库主要以散列的数据类型进行数据存储，如以键值对的方式进行存储。

BerkeleyDB是一类特殊的面向查询进行优化、面向读取进行优化的数据库，主要用于搜索、浏览、更新查询操作，一般对于一次写入数据、多次查询和搜索有很好的效果。BerkeleyDB不支持事务型数据库(MySQL、MariDB、Oracle等)所支持的高并发的吞吐量以及复杂的事务操作。

## 二、初始化环境

初始化环境，如下：

> ntpdate -u [ntp.api.bz](http://ntp.api.bz) && sed -i '/SELINUX/s/enforcing/disabled/' /etc/selinux/config && setenforce 0&& systemctl disable firewalld.service && systemctl stop firewalld.service && shutdown -r now

注：shutdown -r now 可能是不必要的吧

## 三、安装OpenLDAP

使用如下命令安装OpenLDAP：

> yum -y install openldap compat-openldap openldap-clients openldap-servers openldap-servers-sql openldap-devel migrationtools

查看OpenLDAP版本，使用如下命令：slapd -VV

## 四、配置OpenLDAP

OpenLDAP配置比较复杂牵涉到的内容比较多，接下来我们一步一步对其相关的配置进行介绍。

注意:从OpenLDAP2.4.23版本开始所有配置数据都保存在/etc/openldap/slapd.d/中，建议不再使用slapd.conf作为配置文件。

#### 4.1 配置OpenLDAP管理员密码

设置OpenLDAP的管理员密码:

> slappasswd -s 123456 会生成一串加密的密钥，如：{SSHA}dXgO/Ipy5SQiKFZ0u7m79Xo7uzKIr038。保存下等会我们在配置文件中会使用到。

#### 4.2 修改olcDatabase={2}hdb.ldif文件

假设我们测试用域名是：banyuan.club

vim /etc/openldap/slapd.d/cn=config/olcDatabase={2}hdb.ldif

新增一行

> olcRootPW: {SSHA}dXgO/Ipy5SQiKFZ0u7m79Xo7uzKIr038，

然后修改域信息如下：

> olcSuffix: dc=banyuan,dc=club olcRootDN: cn=root,dc=banyuan,dc=club

注意：其中cn=root中的root表示OpenLDAP管理员的用户名，而olcRootPW表示OpenLDAP管理员的密码。

#### 4.3 修改olcDatabase={1}monitor.ldif文件

vim /etc/openldap/slapd.d/cn=config/olcDatabase={1}monitor.ldif

然后修改olcAccess信息如下

> olcAccess: {0}to * by dn.base="gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth" read by dn.base="cn=root,dc=banyuan,dc=club" read by * none

注：变更部分在dn.base部分，这里是和OpenLDAP管理员相关的信息。

验证OpenLDAP的基本配置，使用如下命令： slaptest -u，只要显示：config file testing succeeded. 即为成功。

#### 启动OpenLDAP服务，使用如下命令：

> systemctl enable slapd systemctl start slapd systemctl status slapd

OpenLDAP默认监听的端口是389，测试之：

> netstat -antup | grep 389

注：为了让外部访问阿里云，需要登录阿里云控制台，允许389端口开放