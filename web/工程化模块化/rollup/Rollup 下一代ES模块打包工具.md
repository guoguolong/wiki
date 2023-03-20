[toc]
Vue，React，D3等众多知名项目都使用rollup进行构建打包，为什么没有选择我们项目中经常使用的webpack呢？这篇文章主要分析下rollup同webpack相比打包的优势以及它的一些基本使用方式。

## 背景

rollup从设计之初就是面向`ES module`的，它诞生时AMD、CMD、UMD的格式之争还很火热，作者希望充分利用`ES module`机制，构建出`结构扁平`，`性能出众`的类库。

## ES module机制

ES module的设计思想是尽量的`静态化`，使得`编译时就能确定模块的依赖关系`，以及输入和输出的变量。CommonJS 和 AMD 模块，都只能在`运行时`确定这些东西，举例来说：

1. ES import只能作为模块顶层的语句出现，不能出现在 function 里面或是 if 里面。

2. ES import的模块名只能是字符串常量。
3. 不管 import 的语句出现的位置在哪里，在模块初始化的时候所有的 import 都必须已经导入完成。
4. import binding 是 immutable 的，类似 const。比如说你不能 import { a } from './a' 然后给 a 赋值个其他什么东西。

这些设计虽然使得灵活性不如`CommonJS的require`，但却保证了 ES modules 的依赖关系是确定的，和运行时的状态无关，从而也就保证了ES modules是可以进行可靠的静态分析的。

## rollup对比webpack

我们通过一个案例看一下`webpack`和`rollup`打包后的代码结构。

```js
// 源文件：
// index.js
import a from './a.js'
import b from './b.js' 

export default () => {
  console.log(a()
}

// a.js
export default () => 'a'

// b.js
export default () => 'b'
```

webpack打包生成：

```js
(function(modules) { // webpackBootstrap
  // 大量的runtime代码
  // ...
})
({
  "./src/a.js":
  (function(module, __webpack_exports__, __webpack_require__) {

  "use strict";
  eval(`省略模块代码`);

  }),

  "./src/b.js":
  (function(module, __webpack_exports__, __webpack_require__) {

  "use strict";
  eval(`省略模块代码`);

  "./src/index.js":
  (function(module, __webpack_exports__, __webpack_require__) {

  "use strict";
  eval(`省略模块代码`);
  })
})
```

rollup打包生成：

```js
var test = (function () {
  'use strict';
  var a = () => 'a';
  var index = () => {
    console.log(a());
  };
  return index;
}());
```

首先，webpack致力于复杂SPA的模块化构建，优势在于：
1. 通过loader处理各种各样的资源依赖
2. HMR模块热替换
3. 按需加载
4. 提取公共模块

rollup致力于打造性能出众的类库，有如下优势：

1. 编译出来的代码`可读性好`
2. rollup打包后生成的bundle内容十分`干净`，没有什么多余的代码，只是将各个模块按照依赖顺序拼接起来，所有模块构建在一个函数内（Scope Hoisting）, 执行效率更高。相比webpack(webpack打包后会生成__webpack_require__等runtime代码)，rollup拥有无可比拟的性能优势，这是由依赖处理方式决定的，`编译时依赖处理（rollup）自然比运行时依赖处理（webpack）性能更好`
3. 对于ES模块依赖库，rollup会静态分析代码中的 import，并将排除任何未实际使用的代码(tree-shaking，下文会有具体解释)
4. 支持程序流分析，能更加正确的判断项目本身的代码是否有副作用(配合tree-shaking)
5. 支持导出`es`模块文件（webpack不支持导出es模块）

当然，rollup也有明显的缺点：
1. 模块过于静态化，HMR很难实现
2. 仅面向ES module，无法可靠的处理`commonjs以及umd依赖`

## webpack和rollup的使用原则

通过以上的对比可以得出，构建`App应用`时，webpack比较合适，如果是`类库（纯js项目）`，rollup更加适合。

webpack构建App的优势体现在以下几方面：

1. 强大的`插件生态`，主流前端框架都有对应的loader
2. 面向App的特性支持，比如之前提到的`HMR`，`按需加载`，`公共模块`提取等都是开发App应用必要的特性
3. 简化Web开发各个环节，包括`图片自动base64，资源缓存（chunkId），按路由做代码拆分，懒加载`等
4. 可靠的依赖模块处理，不像rollup那样仅面向ES module，面临cjs的问题（webpack通过`__webpack_require__`实现各种类型的模块依赖问题）

rollup的优势在于构建`高性能的bundle`，这正是类库所需要的。

## tree-shaking

tree-shaking可以理解为通过工具"摇"我们的JS文件，将其中用不到的代码"摇"掉，属于性能优化的范畴。

tree-shaking较早由`rollup`实现，后来`webpack2`也借助于`UglifyJS`实现了tree-shaking的功能。

tree-shaking的本质是借助`ES module的静态分析`来消除`无用的`js代码，无用代码有以下几个特征：
1. 代码不会被执行到
2. 代码执行的结果不会被用到
3. 代码只影响死变量

rollup打包时的tree-shaking案例：

```js
// add.js:
export default (a, b) => {
  return a + b
}

// index.js:
import add from './add.js'
export default () => {
  add(1, 2)
}

// 打包后bundle.js:
var index = () => {
};
export default index;
```

`add(1, 2)`的执行结果在`index.js`中没有被用到，因此在`bundle.js`中被'摇'掉了。

如果函数中存在`副作用`，那么tree-shaking会失效：

```js
// add.js:
export default (a, b) => {
  window.a = 'a'
  return a + b
}

// ...

// bundle.js:
var add = (a, b) => {
  window.a = 'a';
  return a + b
};

var index = () => {
  add(1, 2);
};

export default index;
```

所以我们尽量不要写带有副作用的代码。

## rollup使用教程

首先在项目中安装rollup：

```js
yarn add rollup -D
```

package.json中加入构建脚本命令：

```json
{
  "scripts": {
    "build": "rollup -c"
  }
}
```

## 核心属性

rollup 支持`命令行`和`JS API`两种调用方式，我们重点来看下命令行配置文件中的几个核心属性：

```js
// rollup.config.js
import resolve from 'rollup-plugin-node-resolve';
import commonjs from 'rollup-plugin-commonjs';
import babel from 'rollup-plugin-babel';

export default [{
  input: 'src/index.js',
  output: {
    file: 'dist/bundle.js',
    format: 'umd',
    name: 'test'
  },
  plugins: [
    resolve(),
    commonjs(),
    babel({
      exclude: 'node_modules/**'
    })
  ]
}]
```

**1. input: 填写打包的入口文件路径**

**2. output.format: 源码构建输出的格式**
1. `iife`: 自执行函数, 可通过 `<script>` 标签加载
2. `amd`: 浏览器端的模块规范, 可通过 RequireJS 可加载
3. `cjs`: Node 默认的模块规范, 可通过 Webpack 加载
4. `umd`: 兼容 IIFE, AMD, CJS 三种模块规范
5. `es`: ES module 规范, 可用 Webpack, Rollup 加载

rollup为了方便类库的使用者进行`tree-shaking`，提供了`es`的构建格式：

```js
// 源文件
// add.js:
export default (a, b) => a + b

// index.js:
import add from './add.js'
export default () => {
  var result = add(1, 2)
  console.log(result)
}

// rollup按照`es`的格式构建：
var add = (a, b) => {
  return a + b
};
var index = () => {
  var result = add(1, 2);
  console.log(result);
};
export default index;
```

可以看出，我们得到了一个`基于ES module规范`的bundle，此时读者可能会有这样的疑问：应用项目中通常会设置babel屏蔽`node_module`目录下的文件，如果将`pkg.main`指向当前`ES module规范的bundle`，项目最终打包后的bundle中会包含`ES module`代码。

为了解决上述问题，rollup最早提出了`pkg.module`，配置导出格式为`es`的文件的路径，打包工具在遇到`pkg.module`字段时，会优先使用。

综上所述，类库通过rollup可以设置打包出`两份文件`，一份`umd(按照实际需要可选其他)`，一份`es`，将它们的路径分别设置为package.json中的`main`和`module`的值。这样就能方便类库的使用者进行`tree-shaking`。

**3. external + output.globals**

rollup通过`external` + `output.globals`来标记外部依赖，以lodash为例：

```js
// rollup.config.js
import resolve from 'rollup-plugin-node-resolve';
import commonjs from 'rollup-plugin-commonjs';
export default [
  {
    input: 'src/index.js',
    output: {
      file: 'dist/bundle.js',
      format: 'iife',
      name: 'test',
      globals: {
        'lodash': '_'
      }
    },
    external: [
      'lodash'
    ],
    plugins: [
      resolve(),
      commonjs()
    ]
  }
]

// index.js
import lodash from 'lodash'
export default () => {
  console.log(lodash)
}

// bundle.js
var test = (function (lodash) {
  'use strict';

  lodash = lodash && lodash.hasOwnProperty('default') ? lodash['default'] : lodash;

  var index = () => {
    console.log(lodash);
  };

  return index;

}(_));
```

**4. plugins**

rollup同时提供了一些插件来解决`压缩`，`babel转换`等问题，这里列举几个常用的插件：

1. `rollup-plugin-alias`: 配置module的别名
2. `rollup-plugin-babel`: 打包过程中使用Babel进行转换, 需要安装和配置Babel
3. `rollup-plugin-eslint`: 提供ESLint能力, 需要安装和配置ESLint
4. `rollup-plugin-node-resolve`: 解析node_modules 中的模块
5. `rollup-plugin-commonjs`: 转换 CJS -> ESM, 通常配合上面一个插件使用
6. `rollup-plugin-replace`: 类似于webpack的DefinePlugin

其他配置见：[rollup官网]([https://www.rollupjs.com/guide/zh#introduction](https://link.zhihu.com/?target=https%3A//www.rollupjs.com/guide/zh%23introduction))

最后感谢您花时间阅读这篇文章，希望这篇文章能对您有所帮助。