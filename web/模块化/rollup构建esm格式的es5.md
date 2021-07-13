rollup配置文件样例：

```javascript
import commonjs from '@rollup/plugin-commonjs';
import babel from "@rollup/plugin-babel";
import resolve from '@rollup/plugin-node-resolve';
import typescript from '@rollup/plugin-typescript';

export default [{
  input: './src/index.ts',
  output: {
    file: 'dist/bundle.js',
    format: 'esm'
  },
  plugins: [
    typescript(),
    resolve(),
    commonjs(),
    babel({
      extensions: [
        '.js', '.jsx', '.ts', '.tsx',
      ],
      babelHelpers: 'runtime',
      exclude: 'node_modules/**', // 防止打包node_modules下的文件
    })
  ]
```

## 输出 ESM 格式

关键语句如下：
```javascript
...
output: {
  file: 'dist/bundle.js',
  format: 'esm'
}
...
```

## 转译es5

要使用插件，关键是使用 babel 插件（@rollup/plugin-babel）

```javascript
plugins: [
  babel({....})
]

```
src/.babelrc 内容如下
```javascript
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "modules": false,
        "useBuiltIns": "usage",
        "corejs": "3",
        "targets": {
          "ie": 10
        }
      }
    ]
  ],
  "ignore": [
    "node_modules/**"
  ]
}
```

默认转译 src目录下的代码为 es5

* `extensions: ['.js', '.jsx', '.ts', '.tsx']` 设置将读取 src/.babelrc配置
* 如果不使用 @rollup/plugin-commonjs ，目标代码将可能包含如下 core-js 依赖：

```javascript
var $$9 = require('../internals/export');
var exec = require('../internals/regexp-exec');
```

* 如果是库的开发（给其他开发者使用），应总是使用 `babelHelpers: 'runtime'`，在.babelrc中添加插件

```javascript
{
  ...
  "plugins": [
    [
      "@babel/plugin-transform-runtime",
      {
        "corejs": 3
      }
    ]
  ]
}
```

目的是使代码更加优化，可参考文章 [@babel/preset-env系列-@babel/plugin-transform-runtime](@babel╱preset-env系列-@babel╱plugin-transform-runtime.md.html)

## 其他插件作用

### @rollup/plugin-node-resolve 

```javascript
// 不配置 @rollup/plugin-node-resolve 插件引入方式
export foo from './foo/index.js'
import bar from './bar/index.js'
// 配置了 @rollup/plugin-node-resolve 插件引入方式
export foo from './foo'
import bar from './bar'
```

### @rollup/plugin-typescript

支持 typescript，默认读取 tsconfig.json里的配置
