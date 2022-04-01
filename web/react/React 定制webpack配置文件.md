使用 create-react-app 生成 React 手脚架项目使用 react-script工具开发和构建（`npm start`间接运行），实际包装了 webpack 脚本配置。

如果想定制 webpack 配置，需要使用  `react-script eject` 将 webpack 配置展开到当前项目下，就可以修改 webpack.config.js了。 

不过上述方法很累赘，如果仅仅是想定制 webpack 的一丁点配置，最好的逻辑是：只对进行定制的配置展开。

比较推崇的工具是：react-app-rewired 和 customize-cra

## react-app-rewired（替代 react-script）

地址：https://www.npmjs.com/package/react-app-rewired

### 1. 安装
```
yarn add react-app-rewired --dev
```
### 2. 创建 config-overrides.js（在项目根目录）

```
module.exports = function override(config, env) {
  return config;
}
```
### 3. 替换react-script（在package.json）

```diff
{
  "scripts": {
-   "start": "react-scripts start",
+   "start": "react-app-rewired start",
-   "build": "react-scripts build",
+   "build": "react-app-rewired build",
-   "test": "react-scripts test --env=jsdom",
+   "test": "react-app-rewired test --env=jsdom",
    "eject": "react-scripts eject"
}
```
修改 config-overrides.js 里的webpack 配置，运行 `npm start` 就可以了

## 使用 customize-cra 为 react-app-rewired 更方便定制webpack 配置

地址：https://www.npmjs.com/package/customize-cra

### 1. 安装
```
yarn add customize-cra --dev
```

### 2. 案例A：支持alias @

#### a. 修改  config-overrides.js

```js
const path = require("path");
const { override, addWebpackAlias } = require('customize-cra');
module.exports = override(
  addWebpackAlias({
    ['@']: path.resolve(__dirname, './src'),
  ),
);
```

#### b. 写一个例子

App.js 里用如下方式引用 App.css

```javascript
import '@/App.css'
```

测试发现等同于 `./App.css` ，同样可以工作。

### 3. 案例B：使支持 less

很不幸，customize-cra自带的 less-loader在最新版的CRA中不工作，所以要安装 customize-cra-less-loader

#### a. 安装 less 和 less-loader

```
yarn add --dev less
yarn add --dev customize-cra-less-loader
```

#### b. config-overrides.js内容如下

```javascript
const addLessLoader = require("customize-cra-less-loader");
module.exports = override(
    addLessLoader()
);
```

#### 新增 src/App.less

```less
@width: 100px;
@height: 100px;

img {
    border: 1px solid green;
    width: @width;
    height: @height;
}
```

#### c. src/App.js 引入 App.less

```javascript
import './App.less';
```

重新运行 `npm start` ，就开看到图片的样式改变了.

[在 create-react-app 中使用 antd](https://ant.design/docs/react/use-with-create-react-app-cn) 也有 关于 react-app-wired 的介绍，请参考。