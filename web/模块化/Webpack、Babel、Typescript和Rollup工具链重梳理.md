 **ES6+ 转译 ES5** 开发网页应用，构建工具多、且链条长。本文重新梳理下几个比较经典、传统（不那么新锐）的构建工具：

* Babel、TypeScript：ES6+（TS/JS） 转 ES5；

* Rollup、Wepack：构建合适的 JS 包格式、还能构建打包CSS、图片等；

上面描述已经揭示出：Babel 和 TypeScript 才是 ES6+ 转 ES5的工具，而 Rollup、Webpack其实是借助 Babel/TypeScript 进行ES6+到ES5的转译，其只作为工程构建链条里的一部分，因此，可以是说 ES6+转ES5 是前端工程构建的核心，也是本文着重要介绍的内容。

ES6+转ES5 的目的是为了使旧版本浏览器（不支持ES6+）能运行 使用ES6+书写的代码，这个所谓的**不支持**分为两部分：

1. 旧版本浏览器不支持的ES6语法，如: let、...操作符
2. 旧版本浏览器不支持的ES6新增函数，如：Promise等 （通常我们说Polyfill，其实就是用来解决这个问题的）

所以，我们下文始终使用以下代码进行转译测试：

**index.js**

```js
const asynFunc = (text) => {
  return new Promise((resolve, reject)=>{
    resolve('Async Call ' + text);
  })
}

const calc = () => {
  let { x, y, ...z } = { x: 1, y: 2, name: 'Allen', addr: 'Mars' };  
  console.log('Calc: ', z)
}

calc();

asynFunc('CheckName').then(resp => {
  console.log(resp)
});
```



## Babel

> 参考 https://babeljs.io/docs/en/usage 
>
> github 例子： https://github.com/guoguolong/fe-tools-tutorial.git 的 babel-tutorial目录

**安装 babel及相关包**

```bash
npm install --save-dev @babel/core @babel/cli @babel/preset-env
```

### 浏览器不支持的语法转译(ES6+ -> ES5)

**babel.config.js**

```js
{
  "presets": ["@babel/preset-env"]
}
```

**编译 index.js**

```bash
npx babel index.js -o dist/index.js
```

**dist/index.js 摘录**

```js
"use strict";
...
function _objectWithoutProperties....
var asynFunc = function asynFunc(text) {
  return new Promise(function (resolve, reject) {
    resolve('Async Call ' + text);
  });
};
var calc = function calc() {
  var _x$y$name$addr = {
      x: 1,
      y: 2,
      name: 'Allen',
      addr: 'Mars'
    },
    x = _x$y$name$addr.x,
    y = _x$y$name$addr.y,
    z = _objectWithoutProperties(_x$y$name$addr, _excluded);
  console.log('Calc: ', z);
};
```

不难看出，代码已经完成了从ES6+到ES5的语法转换（`let`转为`var`，`...做了替代实现）。

但是，如果运行的浏览器已经支持 ...或者 let 操作符，那何必一定转换呢？没错，可以通过修改 babel.config.js 配置，指定目标运行的浏览器

```js
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "targets": {
          "chrome": "58",
        },
      }
    ]
  ]
}
```

重新转译，dist/index.js 代码摘录：

```js
"use strict";
...
const asynFunc = text => {
  return new Promise((resolve, reject) => {
    resolve('Async Call ' + text);
  });
};
const calc = () => {
  let _x$y$name$addr = {
      x: 1,
      y: 2,
      name: 'Allen',
      addr: 'Mars'
    },
    {
      x,
      y
    } = _x$y$name$addr,
    z = _objectWithoutProperties(_x$y$name$addr, _excluded);
....
```

显然代码中ES6关键字 `let` 被保留了，仅仅`...`操作符做了替代实现，这也侧面说明 chrome 58支持`let`但不支持`...`语法。因此，与其说 Babel 等工具可以进行 ES6+ 到 ES5转译，不如说可以做 ES-vx 到 ES-vy 的转译更准确，不过要注意，这里的版本`vx`要大于`vy`。

> ES6+ 各个版本差别何在，参考 https://github.com/sudheerj/ECMAScript-features

### 浏览器不支持的函数转译（Polyfill）

观察前面两次转译，发现Promise 函数都是被保留的，因此在低版本浏览器运行该文件上仍然会报错。这就是前文提到`ES6新增函数`的处理问题，即 Polyfill。

> @babel/ployfill 是早期方案，弃用，这里就不讨论了

修改配置 babel.config.js，useBuiltIns 指定目标代码策略，corejs是polyfill的库的名称，值是该库的版本号。

```js
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "targets": {
          "chrome": "58",
        },
        "useBuiltIns": "usage",
        "corejs": "3"
      }
    ]
  ]
}
```

useBuiltIns指定为`usage`， 会在目标代码顶部包含代码中用到浏览器不支持函数的 corejs 实现。重新转译 index.js，结果片段如下：

```js
"use strict";

require("core-js/modules/es.promise.js");
....
const asynFunc = text => {
  return new Promise((resolve, reject) => {
    resolve('Async Call ' + text);
  });
};
```

与第2次运行的结果差别就一行：`require("core-js/modules/es.promise.js");`，加上 `target` 为空，目标浏览器将默认为最低，使目标代码支持最广泛的浏览器。

结果转译代码可能包含更多 corejs 替代实现

```js
require("core-js/modules/es.object.keys.js");
require("core-js/modules/es.array.index-of.js");
require("core-js/modules/es.symbol.js");
require("core-js/modules/es.object.to-string.js");
require("core-js/modules/es.promise.js");
```

注意，一旦用`usage`方法转译代码，使用转译代码的就要`npm i core-js`，否则运行出错。

除了 usage，useBuiltIns还有另外两个值：

* entry：会把目标浏览器所有需要的替代实现模块require进来。

  这要求代码的入口 JS 文件（我们的例子中 index.js即是入口文件）头部导入 `core-js`

```js
import 'core-js';
  
const asynFunc = (text) => {
  return new Promise((resolve, reject)=>{
    resolve('Async Call ' + text);
  })
}
...
```
运行转译后，目标代码里会有完整目标浏览器环境不支持的函数列表，还包括那些当前代码根本没有使用到的。片段如下：
```js
"use strict";

require("core-js/modules/es.symbol.js");
require("core-js/modules/es.symbol.description.js");
require("core-js/modules/es.symbol.async-iterator.js");
require("core-js/modules/es.symbol.has-instance.js");
require("core-js/modules/es.symbol.is-concat-spreadable.js");
require("core-js/modules/es.symbol.iterator.js");
require("core-js/modules/es.symbol.match.js");
require("core-js/modules/es.symbol.replace.js");
require("core-js/modules/es.symbol.search.js");
....
```

>注：如果有多个文件，要保证只在入口文件顶部添加 import 代码，重复导入可能导致出错。

* false： 默认值，就是不做 polyfill

### Polyfill 方法 2（@babel/plugin-transform-runtime）

https://www.jianshu.com/p/743bb036bf40

？？？不用Polyfill，应用侧如何处理Promise



## Webpack



```bash
npm i webpack webpack-cli -D
```

## 默认构建

```
pnpm exec webpack
```

构建IFEE 目标JS文件，看目标代码，显然不会ES5转译、也不会处理依赖包里的函数`polyfill`

### 使用Babel转译

* 安装依赖

```bash
 npm i babel-loader @babel/preset-env @babel/core core-js -d
```

注：pnpm 必须要安装`@babel/core`、 `core-js`，npm则不需要。

### 1. 仅转译 ES6+语法

* 创建 `webpack.config.js`

```js
const path = require('path');

module.exports = {
  module: {
    rules: [
      { 
        test: /\.m?js$/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env']
          }
        }
      }
    ],
  },
};
```

* 创建 babel.config.js

```js
module.exports = {
  plugins: [
    [
      "@babel/plugin-transform-modules-commonjs"
    ]
  ],
  presets: [
    [
      "@babel/preset-env",
    ]
  ]
}
```

重新运行 `webpack`， 目标代码ES6语法转为ES5语法了。

**注：**如果不用 babel.config.js，可以直接在 webpack.config.js增加 bable-loader options

```diff
module.exports = {
  module: {
    rules: [
      { 
        test: /\.m?js$/,
        use: {
          loader: 'babel-loader',
+          options: {
+            presets: ['@babel/preset-env']
+          }
        }
      }
    ],
  },
};
```

### 2. Polyfill 代码 

* 修改 babel.config.js

  ```diff
  module.exports = {
    presets: [
      [
        "@babel/preset-env",
  +      {
  +        "useBuiltIns": "usage",
  +        "corejs": "3",
  +      }
      ]
    ]
  }
  ```

  源代码里的ES6函数就被polyfill了

### 3. Polyfill npm包里的代码

* 安装依赖： 

```bash
pnpm add @babel/plugin-transform-modules-commonjs`
```

因为例子中使用的 `@guoguolong/babel-tutorial`是 commonjs 格式，Webpack转换IIFE时需要处理。
* 修改 babel.config.js

  ```diff
  module.exports = {
  +  plugins: [
  +    [
  +      "@babel/plugin-transform-modules-commonjs"
  +    ]
  +  ],
    presets:
    ...
  }
  ```
  
* 修改 webapck.config.js

  ```diff
  module.exports = {
    module: {
      rules: [
        { 
          test: /\.m?js$/,
          include: [
  +          /src/,
  +          /node_modules\/@guoguolong\/babel-tutorial/
          ],
          use: {
            loader: 'babel-loader',
          }
        }
      ],
    }
  };
  ```

明确指定用`babel`处理 src、node_modules/@guoguolong/babel-turorial 目录里的ES6语法和`polyfill`。

重新运行 `webpack`，然后用低版本 Firefox 浏览器看是否正确 `polyfill`了的`Promise`函数。

