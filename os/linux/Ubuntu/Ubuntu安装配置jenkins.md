## 1.安装jenkins

为了尝鲜最新版本2.9，所以没有使用apt-get安装，从官网下载jenkins_2.9_all.deb，然后执行

> dpkg -i jenkins_2.9_all.deb

如果没有安装依赖daemon，请先执行 apt-get install daemon在执行dpkg。jenkins是JAVA程序，安装好会默认启动8080端口，由于我的 服务器8080已经被gitlab占用，于是不得不更换为8081端口，我需要手动更改配置文件

> /etc/init.d/jenkins /etc/default/jenkins

注意：不要使用小于1000的端口号，否则可能由于权限问题仍然启动失败。 那么是如何找到jenkins相关文件在什么目录呢？执行

> dpkg -c jenkins_2.9_all.deb

就可以查看到安装后相关文件将放到什么目录 安装好之后，访问http://youhost:8081/ 系统会提示你输入密码，然后安装常用的插件，输入你期望的管理员用户名和密码 就可以常见Job了

## 2.使用jenkins

### a). 创建任务，类型为Freetype project.

1. Source Code Management

> Repository URL： http://gitlab.babyun.cn/backend/pluto.git Credentials：创建对应的用户名密码并选择之

1. Build->Execute shell: 例：打包git下来的代码，存储到/tmp目录下（$WORKSPACE可以通过前面的 dpkg -c jenkins_2.9_all.deb 获知,一般是：/var/lib/jenkins/jobs/<JobName>/workspace）

```
tar -czvf /tmp/build.tar.gz $WORKSPACE/*
cp /tmp/build.tar.gz $WORKSPACE/
```

### b). Run这个job

执行过程是：git pull之后将执行Build里的自定义脚本.

## 3. 使用案例1: 失败job发送 邮件到指定邮箱

首先要全局配置Jenkins->Manage Jenkins->Configure System配置E-mail Notification，一定不要遗漏的是： Use SMTP Authentication->User Name所指定的邮箱地址必须与Jenkins Location->System Admin e-mail address的邮箱地址保持一致！！ 由于众所周知的墙gmail常常不好使，所以用了qq，

```
smtp: smtp.qq.com
用户默认邮件后缀: @qq.com
[X]使用SMTP认证
  - 用户名：740369030
  - 密码： [很奇葩，要进入qq邮箱启用smtp，然后返回一个常常的秘钥]
[X]使用SSL协议
  - SMTP端口：465
```

其次打开Job配置：

```
Post-build Actions
    E-mail Notification:
        Recipients: guojunlong@kaulware.com
```

然后在前面Build-Execute shell中故意写错一个linux命令，那么任务执行失败就会发邮件到guojunlong@kaulware.com

## 4. 使用案例2: 发布程序包到远程主机目录.

最基本的方法当然是调用脚本用rsync之类的复制到目标主机的目标目录，不过在jenkins里的思路是：使用插件

1. 进入Manage Jenkins->Manages Plugins->Avaliable->Artifact Uploaders->Publish Over SSH安装该插件.
2. 进入Manage Jenkins->Configure System->Publish over SSH->SSH Servers增加一个SSH服务器.
3. 进入项目的Confiugre->Post-build Actions
   - Add post-build action->Send build artifacts over SSH

指定build.tar.gz将会去$WORKSPACE目录下寻找该文件并通过SSH复制到远程主机；Exec command这里可以填写要执行的远程命令.