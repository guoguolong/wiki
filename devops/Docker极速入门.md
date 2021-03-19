Mac下安装后，通常需要设置Docker Registry国内镜像，我使用阿里云，登陆阿里云后从Docker产品里找到镜像地址：https://z6hicl73.mirror.aliyuncs.com 填入Daemon->Registry mirrors

# 运行一个最简单的Docker容器

运行一个官方的的镜像(image)：

> docker run hello-world

如果发现本地没有该image，默认从Registry服务器下载，然后再运行.

# image的相关操作

> docker images #列出images

> docker rmi cb5af42ce45a #删除指定ID的image，如果images被容器使用，要先删除容器，除非使用-f参数

# 容器相关

1. 用centos映像启动一个名字为webear的容器，如果本地没有centos镜像，从Docer Registry服务器下载后再启动之

> docker run -tid --name webear centos /usr/sbin/init

1. 进入名字为webear的容器（当然该容器是处于运行状态）.

> docker exec -it webear bin/sh

1. 用centos映像启动一个名字为webear的容器.

> docker run -tid --name webear centos /usr/sbin/init

1. 查看运行的容器.

> docker ps

> docker ps -a

1. 启动一个容器

> docker start webear # webear是容器名字.

1. 停止一个容器

> docker stop webear # webear是容器名字.

1. 删除一个容器

> docker rm webear # webear是容器名字.

1. 删除所有容器

> docker rm -f `docker ps -a -q`

# 制作一个image:

例如，执行docker ps

```
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

603b78d58499        centos              "/usr/sbin/init"    5 minutes ago       Up 5 minutes                            webear
```

你可以在这个上述容器实现安装一些软件，比如httpd。然后把webear容器导出一个image，执行：

> docker commit webear guoguolong/webear:0.1.0

然后执行docker images查看成功与否

```
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE


guoguolong/webear       0.1.0              0195f4647e2e        58 seconds ago      361 MB
```

# 发布image到Docker Registry

> docker login #用你注册到Docker Regsitry的账户登录.

> docker push guoguolong/webear

所有标签的guoguolong/webear都上传服务器docker.io/guoguolong/webear了。

一般情况：image的名字要以"<用户名>/"开头， 这个用户名就是你前面docker login使用的用户名。否则没有权限。

如果你的image的名字不符合这个规范，可以打一个符合规范的标签再上传：

> docker tag webear tuncow/webear docker push tuncow/webear