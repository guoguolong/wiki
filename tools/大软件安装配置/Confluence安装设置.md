## 下载

官网下载confluence的tar包(v6.12.2)

## 安装

解压缩（这里解压到/server/confluence目录），打开该目录下confluence/WEB-INF/classes/confluence-init.properties文件，添加行：

```
confluence.home=/server/confluence
```

## 启动

> $ cd /server/confluence $ ./bin/start-confluence.sh

## 浏览器访问

输入 http://localhost:8090/ 进行安装，期间会要求输入license，导向到官网申请之；进入数据库步骤，如果选择mysql，要依次逆行：

1. 下载mysql驱动放到WEB-INF/lib目录，重新启动confluence服务器
2. 创建数据库时，编码COLLATE 应选：utf8_bin，如：

> CREATE DATABASE IF NOT EXISTS confluence DEFAULT CHARSET utf8 COLLATE utf8_bin; 如果没有创建数据用户，可以创建： GRANT ALL ON *.*TO banyuan@localhost IDENTIFIED BY "Banyuan2019";

1. 事务默认级别应设置为：READ-COMMITTED。具体操作过程可能如下：

   - a.查看mysql的conf文件所在位置：

   > $ mysqld --verbose --help|grep -A 1 'Default options' 可能返回结果：/etc/my.cnf /etc/mysql/my.cnf /usr/local/etc/my.cnf ~/.my.cnf

   - b. 编辑my.conf，如：/usr/local/etc/my.cnf ，在 [mysqld]节增加行：

```
 [mysqld]
 ...
transaction-isolation=READ-COMMITTED
```

然后重启服务。可以进入mysql控制台，执行下面的命令确认是否修改过了。

> show variables like 'transaction_isolation';

以上这些步骤，在实际安装过程中都是有提示的，所以不必特别担心。

安装完毕后，仍会检查mysql的一些设置，比如可能提示如下两个变量设置值：

> 成功最大允许数据包 - max_allowed_packet：一般不小于34m InnoDB 日志文件大小 - innodb_log_file_size：一般不小于256M

> character-set-server = utf8 innodb_log_file_size = 256M max_allowed_packet = 34M

## 破解

破解需要在安装过程中进行，安装进入到授权码页面时，开始破解：

1. 使用【附件】目录confluence-crack.zip解压缩，比如解到/cracked目录。
2. 把confluence的WEB-INF/lib/atlassian-extras-decoder-v2-3.4.1.jar 文件复制到/cracked目录，重命名为atlassian-extras-2.4.jar
3. 运行/cracked目录下的confluence_keygen.jar，点.patch!，依提示选择atlassian-extras-2.4.jar，就可以在/cracked目录看到atlassian-extras-2.4.jar和atlassian-extras-2.4.bak两个文件，这里atlassian-extras-2.4.jar已经是破解好的了；
4. 然后在patch软件里输入安装Web页上的Server Id，点击.gen!产生key
5. 将atlassian-extras-2.4.jar名字改回来：atlassian-extras-decoder-v2-3.4.1，复制回confluence的WEB-INF/lib/目录，重新启动confluence
6. 复制key到Web页，进行下一步

## 邮件服务器配置

直接填写地址、协议死活不工作，不得已配置了JNDI：

A. 在{confluence-install}/conf/server.xml中<Context>的末尾加代码：

```
<Context path="" docBase="../confluence" debug="0" reloadable="false" useHttpOnly="true">
....
<Resource name="mail/BanyuanSMTPServer"
    auth="Container"
    type="javax.mail.Session"
    mail.smtp.host="smtp.banyuan.club"
    mail.smtp.port="465"
    mail.smtp.auth="true"
    mail.smtp.user="admin@banyuan.club"
    password="SemiCircle2019"
    mail.smtp.starttls.enable="true"
    mail.transport.protocol="smtps"
    mail.smtp.socketFactory.class="javax.net.ssl.SSLSocketFactory"
/>
</Context>
```

B. 然后移动{confluence-install}/confluence/WEB-INF/lib/mail-x.x.x.jar到{confluence-install}/lib目录（移动不是拷贝） C. 重启服务器， D. 进入后台邮件服务器配置JNDI服务名为：java:comp/env/mail/BanyuanSMTPServer，注意：SMTP单独配置选项都要为空（可以只能配置用户名，同mail.smtp.user），然后发送测试邮件，通过。

## RestAPI调用

官方文档： https://developer.atlassian.com/server/confluence/confluence-server-rest-api/

例： 获取文章内容：http://localhost:8090/rest/api/content/327696?expand=body.storage 获取文章里宏：http://localhost:8090/rest/api/content/327696/history/2/macro/id/0efe0e6f-e80e-4528-81d8-968f47a87907