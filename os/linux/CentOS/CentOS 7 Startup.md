## 新增用户，使有root权限

以root身份登陆先，然后：

> adduser [someone] passwd [someone] vim /etc/sudoers 增加行： [someone] ALL=(ALL) ALL

## SSH用户登录不断线

> ssh -o ServerAliveInterval=5 -o ServerAliveCountMax=1 [someone]@[hostname]

## 软件安装全记录

> yum install centos-release-scl #安装SCL yum install rh-nginx18 yum install rh-php71

1. 对于bash操作类型的软件，使用scl enable [软件名] bash 比如，php就使用scl enable打开一个shell，如果想用户登录后自动enable，使用SLC软件自带的enale脚本加入到 ~/.bashrc

> source /opt/rh/rh-php71/enable

1. 对于服务类型的软件，直接使用systemctl启停

> systemctl list-unit-files #查看有哪些服务脚本可用 systemctl start rh-php71-php-fpm.service # 找到rh-php71-php-fpm.service， 启动之 systemctl enable rh-php71-php-fpm.service #使服务开机自启动

注：实际脚本貌似在 /etc/logrotate.d/目录

如果要删除scl软件，简单执行完yum remove [软件名]，/opt/rh目录下该软件的目录和内容都还在，不知为啥。 我手贱手动删除了该目录，悲剧来了：再次install提示成功，但/opt/rh目录下根本没有该软件目录， 解决方法是：yum remove后，再使用下述方法彻底删除rpm包： rpm -qa | grep -i 软件名 sudo rpm -e --nodeps 包名 再次yum install安装就好了