Vagrant是一个基于Ruby的工具，用于创建和部署虚拟化开发环境。这篇文章主要是讲解vagrant的使用。本文参考自vagrant官方文档

## I. 安装vagrant(vagrant官网 : http://www.vagrantup.com)

下载virtualbox和vagrant的安装包

安装virtualbox

安装vagrant (path路径的添加一般是自动完成的)

## II. 配置一个vagrant项目

配置vagrant项目的第一步是创建一个VagrantFile.

在你的命令行中创建一个项目目录，并且使用: vagrant init 初始化一个VagrantFile.

```
$ mkdir vagrant_dir
$ cd vagrant_dir 
$ vagrant init
```

这个操作会在vagrant项目目录下创建一个VagrantFile文件

# III. Boxes

vagrant使用一个基础的镜像来快速克隆一个虚拟机。这些基础镜像在vagrant中叫做 boxes。在创建好一个VagrantFile后需要规定在当前Vagrant环境中使用的box. boxes的添加可以通过 vagrant box add 命令来实现

> $ vagrant box add ubuntu/trusty64

这个操作会自动从网络中下载一个名为ubuntu/trusty64的box并添加到当前的vagrant环境中。这儿最好还是先从网络上下载下来相应的box，然后在本地对box进行添加。(自动下载的速度慢)

这儿其实可以看到boxes是由类似于 username/boxName 的方式命名的

在这个网站可以找到很多boxes : https://atlas.hashicorp.com/boxes/search

在本地添加box的方法：(vagrant box add <name> <url>)

把box文件(例如文件名为: ubuntu-trusty64.box)下载后放到当前vagrant项目目录下，然后添加一个名为dev的box到环境中

> $vagrant box add dev ubuntu-trusty64.box

显示类似如下信息表示添加成功：

Successfully added box 'dev' (v0) for 'virtualbox'!

## IV. 启动和使用SSH登录

在命令行中键入

> $ vagrant up

即可以启动当前vagrant环境，这样就可以运行box的虚拟机环境。然后可以使用命令

> $ vagrant ssh

来使用ssh登录到vagrant的box虚拟机环境中进行操作，(一般是 127.0.0.1 : 2222 用户名密码都是vagrant)。

> 注：这儿使用命令vagrant ssh 是启用默认的ssh客户端，如果没有，可以自己选择一个ssh客户端进行登录。我使用的是putty

## V. 同步文件夹

默认情况下，vagrant会共享你当前的vagrant项目目录到你客户机的 /vagrant 文件夹下。我们可以用ssh登录到客户机进行查看

```
$vagrant up

$vagrant ssh

...

vagrant@ubuntu-trusty64~$ ls /vagrant

VagrantFile
```

很简单的实现了虚拟机与主机之间的文件夹同步

> 在VagrantFile文件中我们可以查找到这一行

\#config.vm.synced_folder

我们可以配置这个参数改变虚拟机和主机同步的文件夹( 先去掉前面的 # 号， 第一个参数使主机的目录，第二个参数是虚拟机的目录)

例如:

config.vm.synced_folder “/projects/yee-demo” “/vagrant_data/yee-demo”

## VI. 预配置

创建好一个虚拟机环境后，我们可以通过ssh进入虚拟机安装webserver。但vagrant提供了一种自动配置的功能，会在你使用 vagrant up 的时候自动安装。

我们拿安装apache为例

在VagrantFile所在文件夹下创建一个 [bootstrap.sh](http://bootstrap.sh) 文件，内容如下:

```
#!/usr/bin/env bash

apt-get update

apt-get install -y apache2

if ! [ -L /var/www ]; then

  rm -rf /var/www

  ln -fs /vagrant /var/www

fi
```

然后打开VagrantFile文件进行配置config.vm.provision

config.vm.provision :shell, path: “[bootstrap.sh](http://bootstrap.sh)”

最后通过 vagrant up 启动。如果已经启动，则可以使用 vagrant reload --provision 重新启动并安装好apache

登录ssh，通过 下面命令可以查看效果

```
$ vagrant ssh
...

vagrant@ubuntu-trusty64:~$ wget -qO- 127.0.0.1
```

这个功能的用法就看大家的创造力了

注: 这儿的脚本只是在第一次使用 vagrant up的时候生效

## VII. 网络

vagrant的网络配置有3种方式， 这3中网络模式实际上在VagrantFile文件中都有详细的注释说明的，我这儿也简单说一下。

## VIII. 端口转发(port forwarding)

端口转发允许你指定一个客户机的端口分享到主机的一个端口，这允许你在自己的主机上访问一个端口，但所有的网络流量都是转发到特定的客户机端口上

打开VagrantFile文件，添加如下配置项

> config.vm.network :forwarded_port, guest: 80, host: 4567

再通过vagrant up 启动或者是vagrant reload 重启。然后在你自己的浏览器中载入 http://127.0.0.1:4567 ，你将看到vagrant虚拟机中的apache服务器提供的页面。

## IX . 主机模式

> 私有网络

这个模式将使得客户机提供一个IP 地址，主机可以通过这个IP访问客户机网络，配置VagrantFile文件中的如下内容:

config.vm.network “private_network”, ip: “192.168.33.10”

公有网络

config.vm.network “public_network”

## X. 共享

Vagrant共享使得你可以通过因特网连接共享你的vagrant环境给世界上任何人。

在此之前需要现在 https://atlas.hashicorp.com 注册一个账号，然后通过 vagrant login 命令登录账号，然后就可以使用 vagrant share 就可以共享环境了。这儿我们先不做重点讨论

## XI. 回收

- 挂起

通过 vagrant suspend 将会保存当前客户机的状态并且停止运行。当再次使用 vagrant up 的时候客户机又将恢复之前停止时的状态继续运行

- 停止

vagrant halt 将会关闭客户机系统并且断开客户机的电源。vagrant up 又将启动

- 销毁

vagrant destroy 将会从你的系统中移除客户机的所有使用痕迹，停止客户机，断开电源，并且清空客户机的磁盘。 同样通过 vagrant up 启动

## XII. 其他支持平台

除了我们使用的VirtualBox，Vagrant与很多后端支持平台协同工作，例如： VMware 和AWS 等

在这些平台上使用时，当平台安装完成，只需要在vagrant环境启动的时候采用合适的参数表明其使用的平台便可以。

例如：

在vmware上：

> $vagrant up –provider=vmware_fusion

在AWS上

> $ vagrant up –provider=aws

## XIII Vagrant启动失败：Warning: Authentication failure. Retrying.

1. vagrant up之后通过vagrant init进入虚拟机（guest VM）。
2. 虚拟机上执行ssh-keygen，

> cp ~/.ssh/id_rsa.pub authorized_keys

1. 宿主机（我用的是mac）上在当前用户的家目录创建.ssh目录，把虚拟机上的id_rsa复制过来。
2. 修改Vagrantfile，新增如下行：

```
  config.ssh.private_key_path = "~/.ssh/id_rsa"
  config.ssh.forward_agent = true
```