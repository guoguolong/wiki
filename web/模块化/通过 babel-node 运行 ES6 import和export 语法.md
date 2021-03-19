由于自己经常在本地写一些 js 脚本进行文件处理等工作，常常会使用 import 语法引入模块。但是 Node 在默认情况下是不支持 import 和 export 的。

例如下面的文件

```js
import fs from 'fs';
// do sth...
```

运行脚本后，会发现 import 语法报错

```shell
$ node test.js
```
返回
```javascript
(function (exports, require, module, __filename, __dirname) { import fs from 'fs';
                                                                     ^^

SyntaxError: Unexpected identifier
    at new Script (vm.js:79:7)
    at createScript (vm.js:251:10)
    ...
```

这里我提供一个自己在本地调试 js 代码时常使用的方式：使用 `babel-node` 命令，来运行含有 `import/export` 语法的 js 代码。

> 注意：babel-node 不能用于生产环境！因为 babel-node 会加载更多资源和模块，使得运行环境变「重」。

### 1. 安装 babel-node

`babel-node` 在 Babel 7.x 以后，babel 的模块被被拆分。因此需要安装 `@babel/core` `@babel/node` 两个包来获取。

```shell
$ npm i -g @babel/core @babel/node
```

* 如果希望 `babel-node` 命令在全局可用，使用 `-g` 参数。
* 注意：执行 `babel-node` 命令所在的目录下要有 .babelrc 或者 babel.config.js(on)，否则要加参数

### 2. 安装 presets 并配置 .babelrc 文件

仅单安装 `babel-node` 也没用，运行 js 文件后你会发现依然报错。这是因为 `babel-node` 对 `import` 语法默认也是关闭的，因此需要安装指定的 preset 并配置 `.babelrc` 文件来开启语法支持。


```shell
$ npm i @babel/preset-env --save-dev
```

.babelrc 文件配置

```json
{
  "presets": [ "@babel/preset-env" ]
}
```

### 3. 执行 babel-node

至此经过上述配置，再通过 `babel-node` 即可执行含有 import/export 等 es6 语法的 js 文件。

```shell
$ babel-node test.js
```

最后切记由于性能问题，babel-node 仅限于在本地调试时使用，上线生产环境的代码还是需要使用 babel 进行转换，再使用 node 运行。

## 历史

`babel-node` Babel 7.x 以前，需要通过安装 `babel-cli` 包获得。

```shell
$ npm i -g babel-cli
```

presets安装：截止2019年1月，原有的 `babel-preset-es2015` 写法已经废弃，与之代替的是 `babel-preset-env` 或者 `@babel/preset-env`，目前以后者为推荐。

`babel-preset-env` 写法（仅供了解）

```shell
$ npm i babel-preset-env --save-dev
```

.babelrc 文件配置

```json
{
  "preset": [ "env" ]
}
```


### 参考资料

> [babel-node](https://babeljs.io/docs/en/babel-node)    
> [babel-preset-es2015 -> babel-preset-env](https://babeljs.io/docs/en/env)    
> [@babel/preset-env](https://babeljs.io/docs/en/babel-preset-env)