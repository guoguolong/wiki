# 几个Dockerfile例子

Node 镜像：/project/docker/node/Dockerfile

```

FROM tcr.tucow.io/library/centos:7.2.1511

MAINTAINER Allen Guo "guojunlong@tuniu.com"

RUN yum -y update

RUN curl --silent --location https://rpm.nodesource.com/setup_6.x | bash -

RUN yum -y svn install nodejs npm

RUN npm i cnpm -g

RUN cnpm set registry=http://npm.tuniu.org

RUN npm i pm2 -g

```



Achilles镜像： /project/docker/achilles/Dockerfile

```

#FROM centos:latest

FROM tcr.tucow.org/public/nodejs:v6-latest

MAINTAINER Allen Guo "guojunlong@tuniu.com"

RUN mkdir -p /projects

WORKDIR /projects

RUN svn co --force --username guojunlong --password xxxxx http://svn.github.com/ACHILLES/trunk achilles --force --no-auth-cache

EXPOSE 4006

ENV NODE_ENV prod

ENV LC_ALL=en_US.UTF-8 #解决vim乱码问题

VOLUME ["/opt/tuniu/logs/app"]

# pm2 start pm2.config.json --no-daemon

CMD ["npm run $NODE_ENV:start"]

```

Achilles本地开发镜像：

```

FROM tcr.tuniu.io/public/nodejs:v6.10.0

MAINTAINER Allen Guo "guojunlong@tuniu.com"

RUN mkdir -p /projects

WORKDIR /projects

RUN svn co --force --username guojunlong --password Ailon1102@ http://boy.tuniu.com/svn/MOB/ACHILLES/branch/ achilles --force --no-auth-cache

EXPOSE 4006

ENV NODE_ENV local

#ENV LC_ALL=en_US.UTF-8 #解决vim乱码问题

#VOLUME ["/opt/tuniu/logs/app"]

pm2 start pm2.config.json --no-daemon

CMD ["npm run $NODE_ENV:start"]

```



# 构建Dockerfile

如果编写好的dockerfile存在" /project/docker/achilles"下，那么，可以操作如下：

cd /project/docker/achilles

>docker build -t achilles:1.0.0 . 



说明： -t 后面接软件名称和版本号，格式SoftWare_Name：Tag，最后的那个“.”千万别忽略了。