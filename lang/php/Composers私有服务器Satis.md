## I. 安装环境：PHP 和 Composer

各位Php早就用7了吧？Composer 官网 https://getcomposer.org/ 也已经很清楚了吧，不赘述。

安装好环境后，可以配置国内镜像(建议配置)，加快访问速度：

```
composer config -g repo.packagist composer https://packagist.phpcomposer.com
```

## II. 使用 Satis 搭建私有仓库

Satis也作为一个composer库在 https://packagist.org/packages/composer/satis ，我抽取关键部分供快速阅读：

### 1). 下载statis项目

使用 Composer 自带的建项目功能，这个相当于git clone+composer install+ 运行 post-install 脚本。

```
composer create-project composer/satis my-satis --stability=dev --keep-vcs
```

### 2). 建立配置文件 satis.json

在/path/to/my-satis目录下建立satis.json文件

```
{
    "name": "bootjs/satis",
    "homepage": "http://packagist.bootjs.org",
    "repositories": [{
        "type": "vcs",
        "url": "https://github.com/guoguolong/tspclient.git"
    }],
    "require-all": true
}
```

### 3). 生成仓库列表

```
php bin/satis build satis.json ./web
```

就可以在path/to/my-satis/web/里生成仓库列表了。

可能会报协议错误，默认是禁止 http 方式获取代码。需要 satis.json 配置开启。

```
    "config": {
        "secure-http": false
    }
```

### 4). 配置 Web Server

将 web 目录配置 Web Server 访问（用Nginx）。虚拟域名就是之前我们配置的 homepage : [packagist.bootjs.org](http://packagist.bootjs.org)

访问 [packagist.bootjs.org](http://packagist.bootjs.org) 如图

![image](http://note.youdao.com/yws/res/3841/E5764E766EB74259B90BB0D433039DCB?ynotemdtimestamp=1616162670419)

## III 创建一个composer包，提交到satisfy

```
composer init
```

按照提示一步一步输入，类型选择：library

配置完成后生成 composer.json文件，内容如下：

```
{
    "name": "boojs/csv-parser",
    "description": "A Simple CSV Parser",
    "type": "library",
    "license": "MIT",
    "authors": [
        {
            "name": "Allen Guo",
            "email": "allen.guo@gmail.com"
        }
    ],
    "require": {
        "php": ">=7.1.0"
    }
}
```

- 提交代码到git仓库
- 创建tag（必须滴）

```
$ git tag -a v0.1.0 -m 'add 0.1.0 version....'
$ git push origin v0.1.0
```

提交代码，添加 tag 并推送到服务器。tag 就是包的版本号

## IV. 使用 Satis仓库里的Composer库

只需要在项目的 composer.json 文件的根上添加

```
"repositories": [{
    "type": "composer",
    "url": "http://packagist.bootjs.org"
}],
```

记得配置 secure-http。然后执行

```
composer require bootjs/csv-parser
```

## V. 更多关于satis（不可或缺的配置）

1. 每次library版本更新（新的tag），需要重新执行：

```
php bin/satis build satis.json ./web
```

因此该脚本应放到cron里执行。为了保证cron定时更新respository里私有仓库的元数据信息，可以查看comsposer里的security章节，或者设置相应的git，svn免输入密码，比如git免输入密码：

```
git config --global credential.helper store
```

1. 每次新增library，则需要修改satis.json，增加代码仓库地址，再重新执行 bin/statis脚本
2. library代码可以缓存，satis.json 增加

```
    "archive": {
        "directory": "dist",
        "format": "tar",
        "skip-dev": true
    }
```

再重新执行 bin/satis脚本，那么library代码就缓存到 ./web/dist目录下了。

## VI. 总是使用 satisfy 替代 satis（很重要！！）

官方提供一些satis扩展工具如：https://github.com/ludofleury/satisfy 可以用Web界面修改satis.json，然后就可以方便完成V的配置。

Satisfy 内置了 satis，所以安装好 satisfy，像 statis 一样创建satis.json发布到web目录(Satisfy 采用php开发，所以web目录要配置PHP)，安装方法：

```
composer create-project playbloom/satisfy  --stability=dev
```

http://packagist.bootjs.org 访问原来的satis静态页
http://packagist.bootjs.org/admin 访问satis.json管理页

应该总是为 satisfy 管理入口设置权限，修改 app/config/parameters.yml :

```
admin.auth: true
admin.users:
    admin:
        password: 123456
        roles: 'ROLE_ADMIN'
```

重新访问 http://packagist.bootjs.org/admin ， 将要求输入用户名和密码，输入 admin 和 123456 才能进入管理。