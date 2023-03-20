[toc]
## NPM 贡献代码

### 1. 首先你要注册npmjs.com一个账户吧！

```
我的账号是：guoguolong/cold2019winter
```

### 2. 如果你设置过国内镜像，请删除之：

查看当前registry：

> npm get registry

删除当前registry

> npm config delete registry

默认恢复为：https://registry.npmjs.org/

### 3. npm login

用你注册于npmjs.com的用户名进行登陆

### 4. 发布你准备好的npm包

> cnpm pub

## 修改NPM镜像

修改npm的registry为淘宝镜像([npm.taobao.org](http://npm.taobao.org))

镜像使用方法（三种办法任意一种都能解决问题，建议使用第三种，将配置写死，下次用的时候配置还在）:

### 1.通过config命令

```
npm config set registry https://registry.npm.taobao.org 
```

npm info underscore （如果上面配置正确这个命令会有字符串response）

### 2.命令行指定

```
npm --registry https://registry.npm.taobao.org info underscore 
```

### 3.编辑 ~/.npmrc 加入下面内容

```
registry = https://registry.npm.taobao.org
```

搜索镜像: https://npm.taobao.org

建立或使用镜像,参考: https://github.com/cnpm/cnpmjs.org