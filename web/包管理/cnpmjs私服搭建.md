[toc]
## 下载安装

https://github.com/cnpm/cnpmjs.org 下载 3.0.0-alpha.15 版本，然后参考官方 README.md 中 [How to deploy](https://github.com/cnpm/cnpmjs.org/wiki/Deploy)，遵循如下步骤安装（官方版的精要）：

1. 导入sql到mysql数据库
2. 添加配置文件 config/config.js
```js
module.exports = {
    debug: false,
    enableCluster: true, // enable cluster mode
    enablePrivate: false, // enable private mode, only admin can publish, other user just can sync package from source npm
    database: {
	db: 'cnpmjstest',
        host: 'localhost',
        port: 3306,
        username: 'cnpmjs',
        password: 'cnpmjs123'  
    },
    admins: {
      admin: 'admin@cnpmjs.org',
    },
    syncModel: 'exist'// 'none', 'all', 'exist'
  };
```
特别注意：这里的 enablePrivate 应为false，我认为这才是一般的设置。

3. 安装依赖
```
npm install --build-from-source --registry=https://registry.npm.taobao.org --disturl=https://npm.taobao.org/mirrors/node
```
4. 运行
```
 npm run start
```

5. 检查
```
#open registry and web
# registry
open http://localhost:7001
# web
open http://localhost:7002
```

检查过程中如果出错，通常页面显示“Internal Server Error”，这时是要通过查看 config/index.js 找到日志所在路径（默认~/.cnpmjs.org）。

最常见的错误是Mysql版本过高，需要修改密码认证方式为 msyql_native_password ：
```
ALTER USER 'cnpmjs'@'localhost' IDENTIFIED WITH mysql_native_password BY 'cnpmjs123';
```

## 作为镜像仓库使用

* 下载 npm 包（如lodash）
```
npm --registry=http://localhost:7001 i lodash
```
安装到本地的同时，也默认缓存在 ~/.cnpmjs.org/nfs 子目录

如果私仓是你常用的npm服务器，你应该总是设置它为默认registry
```
npm config set registry http://localhost:7001
```

## 作为私有仓库使用

### 1. 创建npm包项目  
package.json如下：
```
{
  "name": "@cnpmtest/light",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "Allen",
  "license": "ISC"
}
```
其中 name 的 @cnpmtest 前缀是必要的，正如 config/index.js的配置
```
...
    scopes: [ '@cnpm', '@cnpmtest', '@cnpm-test' ],
...
```
表示只有以'@cnpm', '@cnpmtest', '@cnpm-test' 前缀命名的包才可以被发布。

### 2. 发布npm包项目

#### a. 以管理员身份登录npm服务器

默认有一个管理员账户admin，email是admin@cnpmjs.org（见 config/index.js配置，当然如果配置了config/config.js，会覆盖index.js的同名项）.

```
npm login
```
提示输入用户名、密码和邮箱，用户名和密码必须吻合index.js里的配置，密码则是任意非空字符串，牢记该字符串，以后登录使用第一次设置的密码字符串作为密码。
>Username: admin  
>Password:   
>Email: (this IS public) admin@cnpmjs.org  
>Logged in as admin on http://localhost:7001/.  

#### b. 添加一个用户 allen
```
npm adduser allen
```
>Username: allen  
>Password:   
>Email: (this IS public) allen@cnpmjs.org  
>Logged in as allen on http://localhost:7001/.  

同上，第一输入的密码就是以后用到的密码要牢记，创建好用户就默认以该用户的身份登录了。

#### c. 发布 npm 包
进入包所在目录，执行：
```
npm pub
```
第一个成功发布 npm 包到 cnpm 仓库的人自动成为包的 owner。

注：通常你要设定config.js中：
```
enablePrivate: false,
```
否则，仅管理员才有权限发布 npm 包到cnpm仓库。

### 3. 其他人维护存在的npm包

#### a. 赋予目标用户 owner 权限
现在如果用户judy想要具备发布 @cnpmtest/light 的权限，则需要cnpm仓库管理员或包的 owner 将加入该包的 maintainer，就可以发布了。

当前包的 owner 是 allen，以 allen 身份登录cnpm仓库，运行（假设 judy 账户已经建立）：
```
npm owner add judy
```

### b. 以目标用户身份登录发布

```
npm login
npm pub
```

## 定制用户认证

作为私服，通常最需要定制的地方就是用户认证，典型情况是使用组织内的ldap服务器作为cnpm服务器的认证方式。定制流程为：

### 1. config/config.js设置userService为true
```
userService: true
```

### 2. 修改 services/user.js
```
if (!config.userService) {
    var DefaultUserService = require('./default_user_service');
    config.userService = new DefaultUserService();
    config.customUserService = false;
} else {
    config.userService = require('./your_user_service'); // 新增的代码
    config.customUserService = true;
}
```

### 3. services/your_user_service.js 代码可能如下：
```
const ldapjs = require('ldapjs');
const isAdmin = require('../lib/common').isAdmin;

function authLDAP(username, password) {
    return new Promise(function (resolve, reject) {
        let client = ldapjs.createClient({
            url: 'ldap://$ldap_server'
        });
        client.bind(username + '@YourOrg.com', password, err => {
            client.destroy();
            if (err) {
                return resolve(false);
            }
            resolve(true);
        });
        client.on('error', err => {
            console.error('LDAP账户认证失败', err);
            resolve(false);
        });
    });
}

module.exports = {
    /**
     * @description 域账户鉴权
     * @author allen@YourOrg.com
     */
    auth: function * auth(username, passord) {
        let userAuth = yield authLDAP(username, passord);
        let userData = null;
        if (userAuth) {
            userData = {
                login: username,
                email: `${username}@YourOrg.com`,
                site_admin: isAdmin(username)
            }
        }
        return userData;
    },

    /**
     * @description 域账户模式下get方法简单实现
     * @author allen@YourOrg.com
     */
    get: function *get(username) {
        return {
            login: username,
            email: `${username}@YourOrg.com`,
            site_admin: isAdmin(username)
        }
    },

    /**
     * @description 域账户模式下list方法简单实现
     * @author allen@YourOrg.com
     */
    list: function *list(usernames) {
        if (!Array.isArray(usernames)) {
            usernames = [usernames];
        }
        return usernames.map(username => ({
            login: username,
            email: `${username}@YourOrg.com`,
            site_admin: isAdmin(username)
        }));
    },

    /**
     * @description 域账户模式下search方法不实现，仅返回空数组
     * @author allen@YourOrg.com
     */
    serach: function *serach(query, options) {
        return [];
    }
};
```

### 4. 用你组织的用户登录试试吧