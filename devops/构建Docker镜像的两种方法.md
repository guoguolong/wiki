
## I. 用 Dockerfile 构建镜像


### 一个Dockerfile例子

假设放在 /project/docker/racoon/Dockerfile 

```Dockerfile
# 基于 node 镜像
FROM my.dockerhub.io/public/nodejs:v6-latest
MAINTAINER Allen Guo "elon.guo@gmail.com"
RUN mkdir -p /projects
WORKDIR /projects
# 下载项目代码
RUN svn co --force --username allenguo --password xxxxx http://svn.github.com/planet/trunk racoon --force --no-auth-cache

EXPOSE 4900
ENV NODE_ENV prod
ENV LC_ALL=en_US.UTF-8 #解决vim乱码问题
VOLUME ["/opt/my/logs/app"]
# 运行命令
CMD ["npm run $NODE_ENV:start"]
```

### 构建Dockerfile

如果编写好的dockerfile存在" /project/docker/racoon"下，那么，可以操作如下：

cd /project/docker/racoon

>docker build -t racoon:1.0.0 . 

说明： -t 后面接软件名称和版本号，格式SoftWare_Name：Tag，最后的那个“.”千万别忽略了。

下面是构建一个名字为 racoon，版本号为 latest 的镜像
>docker build -t racoon:latest. 

### 运行构建好的镜像

运行名字为 racoon，版本号为 1.0.0 的镜像

> docker run racoon:1.0.0 .

暴露端口 4900 到外部 80 端口
> docker run -p 4900:80 racoon:1.0.0 .


## II. 用正在运行的 container 构建镜像


例如，执行 `docker ps`

```
CONTAINER ID    IMAGE       COMMAND         CREATED         STATUS      PORTS    NAMES
603b78d58499    centos  "/usr/sbin/init"  5 minutes ago  Up 5 minutes   webear
```

你可以在这个上述容器实现安装一些软件，比如httpd。然后把webear容器导出一个image，执行：

> docker commit webear guoguolong/webear:0.1.0

然后执行docker images查看成功与否

```
REPOSITORY          TAG             IMAGE ID        CREATED        SIZE
guoguolong/webear  0.1.0          0195f4647e2e    58 seconds ago  361 MB
```

## III. 发布image到Docker Registry

### 注册登录 Docker Regsitry.
> docker login

### 发布本地镜像
> docker push guoguolong/webear

所有标签的guoguolong/webear都上传服务器docker.io/guoguolong/webear了。

一般情况：image的名字要以 `<用户名>/` 开头， 这个用户名就是你前面docker login使用的用户名。否则没有权限。

如果你的image的名字不符合这个规范，可以打一个符合规范的标签再上传：

> docker tag webear tuncow/webear docker push tuncow/webear