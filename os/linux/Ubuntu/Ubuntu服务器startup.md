## I. 基础操作.

```
1. 修改默认编辑器
update-alternatives --config editor
2. apt-get update / apt-get upgrade
3. 修改容易记忆的主机名(如果阿里ECS购买时没有设置好)
  1).  vim /etc/hostname
  2).  vim /etc/hosts
```

## II. 创建账户

```
1. adduser <your name>
2.给用户添加sudo权限
    root登录，执行visudo, 增加类似的行： allen  ALL=(ALL:ALL) ALL
3.禁止root账户登录
    vim /etc/ssh/sshd_config
    PermitRootLogin yes修改为no
4.设置自动登录
     scp haorui@120.27.135.213:~/.ssh/id_rsa .ssh-hr
     ssh -i .ssh-hr/id_rsa haorui@120.27.135.213
    参考： http://koda.iteye.com/blog/898262
5.加长登录时间
    参考笔记：《如何设置SSH服务中终端的超时时间或不超时》
```

## III. 安装常用软件

```
Nginx + PHP + MySQL
```