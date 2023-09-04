### 下载

官网下载tar.gz绿色版

### 设置与安装

假设安装在/projects/jira目录：

1. atlassian-jira/import/startupdatabase.xml 无权限 手工创建atlassian-jira/import/目录，设置 777
2. 复制mysql驱动到lib目录
3. 修改 WEB-INF/classes/jira-application.properties，设置jira.home=/projects/jira

注：安装到最后一步总是被卡住：不能创建管理员；但反复尝试，竟然能通过，无奈。

### 启动服务器

bin/start-jira.sh

### 浏览器访问

http://localhost:8080

### 破解

先看【附件】目录下的jira792-crack 操作，然后淘宝购买破解，获得license