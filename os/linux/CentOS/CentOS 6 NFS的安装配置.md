## 1. 服务器

1. yum install nfs-utils rpcbind
2. 假设使用已经存在的目录/mnt/zeus/files/prod-files为nfs共享目录 vim /etc/exports #此文件默认不存在

```
/mnt/zeus/files/prod-files 114.215.170.20(rw,sync,no_root_squash,fsid=0)
```

rw允许114.215.170.20客户端读写等，其他参数含义见文末说明。

1. 然后使之开机自动启动

```
chkconfig nfs on
/etc/init.d/rpcbind start
/etc/init.d/nfs start
```

## 2. 客户端

1. yum install nfs-utils rpcbind
2. 查看是否能访问nfs服务 showmount -e <前面配置的服务器ip地址或域名> #假设服务器域名为：[www.babyun.net](http://www.babyun.net)
3. 启动nfs客户端：

> /etc/init.d/rpcbind start

1. 装载nfs服务器共享目录到本地指定目录

> mount -t nfs [www.babyun.net](http://www.babyun.net):/mnt/zeus/files/prod-files /mnt/zeus/files/prod-files/

1. 可选配置：配置开机自动挂载: vim /etc/fstab

> 192.168.1.75:/opt/centos6/ /opt/centos6/ nfs nodev,ro,rsize=32768,wsize=32768 0 0 # 添加

1. 卸载挂载

> umount /mnt/zeus/files/prod-files

```
NFS主要有3类选项：
访问权限选项
设置输出目录只读：ro
设置输出目录读写：rw
用户映射选项
all_squash：将远程访问的所有普通用户及所属组都映射为匿名用户或用户组（nfsnobody）；
no_all_squash：与all_squash取反（默认设置）；
root_squash：将root用户及所属组都映射为匿名用户或用户组（默认设置）；
no_root_squash：与rootsquash取反；
anonuid=xxx：将远程访问的所有用户都映射为匿名用户，并指定该用户为本地用户（UID=xxx）；
anongid=xxx：将远程访问的所有用户组都映射为匿名用户组账户，并指定该匿名用户组账户为本地用户组账户（GID=xxx）；
其它选项
secure：限制客户端只能从小于1024的tcp/ip端口连接nfs服务器（默认设置）；
insecure：允许客户端从大于1024的tcp/ip端口连接服务器；
sync：将数据同步写入内存缓冲区与磁盘中，效率低，但可以保证数据的一致性；
async：将数据先保存在内存缓冲区中，必要时才写入磁盘；
wdelay：检查是否有相关的写操作，如果有则将这些写操作一起执行，这样可以提高效率（默认设置）；
no_wdelay：若有写操作则立即执行，应与sync配合使用；
subtree：若输出目录是一个子目录，则nfs服务器将检查其父目录的权限(默认设置)；
no_subtree：即使输出目录是一个子目录，nfs服务器也不检查其父目录的权限，这样可以提高效率；
```