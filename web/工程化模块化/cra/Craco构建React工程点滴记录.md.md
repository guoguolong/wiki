[toc]
# Craco构建 React 工程点滴

## 编译node_modules 下的包

使用 https://www.npmjs.com/package/craco-babel-loader 插件

```bash
npm i craco-babel-loader
```

craco.config.js配置

```js
const rewireBabelLoader = require("craco-babel-loader");
module.exports = {
  plugins: [
    {
      plugin: rewireBabelLoader, 
      options: { 
        includes: [path.resolve("node_modules/XXX")], //put things you want to include in array here
      }
    }
  ]
 }
```

注：如果手动修改 `node_modules` 下包里的 jsx 文件，如果想立即生效，需要删除缓存、重新启动：
```bash
rm -rf node_modules/.cache/* && npm run start
```

