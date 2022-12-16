以下假设开发一个用 TypeScript(ts|tsx) 写的 npm 包，名字是：@allen/widgets。

## TypeScript 包的开发（仅 ts|tsx 源码）

### 目录布局
```
/root
  |_ src 
  |    |_ index.ts
  |    |_ Name1.tsx
  |    |_ Name2.tsx
  |_ package.json
```

### package.json 入口

```json
{
  "name": "@allen/widgets",
  "module": "src/index.ts",
}
```

### src/index.ts
```javascript
import Name1 from './Name1';
import Name2 from './Name2';

export {
  Name1,
  Name2
}
```

如果调用方直接导入 .ts|tsx 文件，这已经足够了。直接 npm pub。


## TypeScript 包的调用（直接导入 .tsx|tsx）

### 1. babel.config.js (babel 7)

```javascript
module.exports = {
  "presets": [
    "@babel/env",
    "@babel/react",
    "@babel/preset-typescript"
  ],
  "plugins": [
    "@babel/plugin-proposal-class-properties",
    [
      "@babel/plugin-transform-runtime",
      {
        "useESModules": true,
        "regenerator": false
      }
    ]
  ]
}
```

### 2. tsconfig.json
```
{
  "extends": "./tsconfig.paths.json", // 省略之
  "compilerOptions": {
    "jsx": "react",
    "baseUrl": ".",
    "target": "es5",
    "allowJs": true,
    "skipLibCheck": true,
    "strict": true,
    "declaration": false,
    "forceConsistentCasingInFileNames": true,
    "isolatedModules": true,
    "lib": [
      "dom",
      "dom.iterable",
      "esnext"
    ],
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "module": "esnext",
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "noEmit": true
  },
  "include": [
    "src/**/*"
  ]
}
```

### 3. webpackage.config.js

增加或修改规则(`module.rules == ts(x)`)的 loader，使包含 `/node_modules/` 路径，如果是修改已有的规则，改规则已存在 `exclude:/node_modules/`设定, 删除之。

下面是 single-spa 修改规则的例子
```javascript
const singleSpaDefaults = require('webpack-config-single-spa-react-ts');

module.exports = (webpackConfigEnv) => {
  const defaultConfig = singleSpaDefaults({
    orgName: 'allen',
    projectName: 'mars',
    webpackConfigEnv,
  });
  const APP_PATH = __dirname;
  
  // 以下是关键代码：修改默认 loader 规则.
  + defaultConfig.module.rules.map(rule => {
  +   if (rule.test) {
  +     if (rule.test.toString() == /\.(js|ts)x?$/) {
  +       delete rule.exclude;
  +       rule.include = [
  +         path.resolve(APP_PATH, 'src'),
  +         path.resolve(APP_PATH, 'test'),
  +         path.resolve(APP_PATH, 'node_modules/@allen/widgets')
  +       ]
  +     }
  +   }
  + })
  ... ...
```

### 4. 代码调用

```javascript
import {Name1, Name2} from '@allen/widgets'
```

## 真正 TypeScript 包的开发（ts|tsx构建为.js后供调用）

TypeScript 是开发时环境，暴露给外部接口的最佳实践是：把 `.ts|tsx` 编译为 `js`）：

### 初始化 TypeScript

```bash
npm i typescript -D
npx tsc --init # 生成 tsconfig.json
```

### 增加 webpack.config.js
webpack 配置文件，旨在把 ts 编译成 js。

### 增加 .babelrc 
weback 构建会调用 babel，这里是babel配置。

### 最终目录布局

```
/root
  |_ src 
  |    |_ index.ts
  |    |_ Name1.tsx
  |    |_ Name2.tsx
  |_ package.json
  |_ .babelrc
  |_ tsconfig.json
  |_ webpack.config.js
```

### webpack.config.js

webpack 构建 src 下的源代码到 es 目录，构建好后 es 目录类似如下：

```
/root
  |_ es
     |_ index.js
     |_ Name1.js
     |_ Name2.js
```

然后 package.json 的入口修改为 es/index.js。

### package.json 入口

```json
{
  "name": "@allen/widgets",
  "module": "es/index.js",
}
```

### es/index.js
```javascript
import Name1 from './Name1';
import Name2 from './Name2';

export {
  Name1,
  Name2
}
```

## TypeScript 包的调用（导入 .js 使用）

调用方直接 `npm install`，然后调用 

```javascript
import {Name1, Name2} from '@allen/widgets'
```

完全不必理会自己本身的 .babelrc、tsconfig、webpack.config.js 的设置。