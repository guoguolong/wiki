```
apt install mysql-client-5.7 
apt install mysql-server-5.7 
apt install libmysqlclient-dev
apt install redis # v3.0.6
apt install default-jdk
```

出错： Errors were encountered while processing: /var/cache/apt/archives/openjdk-9-jdk_9~b114-0ubuntu1_amd64.deb E: Sub-process /usr/bin/dpkg returned an error code (1)

```
原因是依赖问题，解决方法：
```

$ sudo dpkg --configure -a $ sudo dpkg -i --force-overwrite /var/cache/apt/archives/openjdk-9-jdk_9~b114-0ubuntu1_amd64.deb $ sudo apt-get -f install

```
apt install git
apt install nginx

# 安装openldap
# 安装 nodejs
curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
sudo apt-get install -y nodejs
```