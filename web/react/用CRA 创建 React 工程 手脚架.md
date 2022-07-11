# 用CRA 创建 React 工程 手脚架

## CRA 创建 typescript 工程骨架

```bash
npx create-react-app magic-mooc --template typescript
```

然后运行

```bash
cd magic-mooc
npm start
```

> **注：当前 CRA的版本是 5.0.1**, 以下命令操作假设已经预先进入了 `magic-mooc`目录

## 用 Craco定制 Webpack 配置

安装 craco 

```bash
npm i @craco/craco@alpha -D
```

> 将会安装 @craco/craco@7.0.0-alpha.3 位了支持 CRA 5

修改 `package.json`里的 `scripts`属性

```diff
/* package.json */
"scripts": {
-   "start": "react-scripts start",
-   "build": "react-scripts build",
-   "test": "react-scripts test",
+   "start": "craco start",
+   "build": "craco build",
+   "test": "craco test",
}
```

然后在项目根目录创建一个 `craco.config.js` 用于修改默认配置。

```js
/* craco.config.js */
module.exports = {
  // ...
};
```

运行 `npm start` 试试。

### 使支持 less

安装 `craco-less` 并修改 `craco.config.js` 文件如下。

```bash
$ npm i craco-less -D
```

```javascript
const CracoLessPlugin = require('craco-less');

module.exports = {
  plugins: [
    {
      plugin: CracoLessPlugin,
      options: {
        lessLoaderOptions: {
          lessOptions: {
            modifyVars: { '@primary-color': '#1DA57A' },
            javascriptEnabled: true,
          },
        },
      },
    },
  ],
};
```

> 配置中 `options`是可选的，对于 `antd` 定制主题则很必要，更多 `antd` 请参考： https://ant.design/docs/react/use-with-create-react-app-cn


## 用 customize-cra 定制webpack 配置

```bash
npm i react-app-rewired customize-cra -D
```

* 在项目根目录创建 `config-overrides.js`，内容如下：

```javascript
const { override } = require("customize-cra");
module.exports = override({});
```
*. 替换 `react-script`（在 `package.json`）

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
运行 `npm start` 试试。接下里就可以放便修改 `config-overrides.js` 里的webpack 配置了。

### 使支持 less

```bash
npm i less less-loader customize-cra-less-loader -D
```
修改 `config-overrides.js`：
```javascript
const { override } = require('customize-cra');
const addLessLoader = require('customize-cra-less-loader');

module.exports = override(
  addLessLoader({
    lessLoaderOptions: {
      lessOptions: {
        modifyVars: {
          '@primary-color': '#9DA57A',
        },
        javascriptEnabled: true,
      },
    },
  })
);
```
> lessLoaderOptions参数不是必要的，典型情况下`antd` 定制主题需要。

更详细请参考 [用 customize-cra 定制 CRA工程配置](用 customize-cra 定制 CRA工程配置.html)



## 使用其他常用库

### 1. 支持 redux 及相关

https://redux.js.org/ 基础版  
https://react-redux.js.org React集成版 (直接使用足矣)

```bash
npm i redux react-redux  # 已经依赖了 @types/react-redux，不必单独安装
```

* **支持 redux 异步 dispatch**

```bash
npm i redux-thunk
npm i @types/redux-thunk -D # support typescript

npm i redux-saga
npm i @types/redux-saga -D
```

### 2. 支持 react-router

https://reacttraining.com/react-router/

react-router-dom  替代 react-router，直接安装可。

```bash
npm i react-router-dom
npm i @types/react-router-dom -D
```

### 3. 远程调用：axios

```bash
npm i axios
```

### 4. 使用表单

```bash
npm i formik yup
```

### 5. 使用 jest

create-react-app 已经集成了:
```
@testing-library/react
@testing-library/jest-dom --> @testing-library/dom
@testing-library/user-event
```

### 6. 国际化

```shell
npm i react-i18next
```

### 7. AntD
```bash
npm i antd
```

### 8. markdown

```
npm i markdown-it
npm i @types/markdown-it -D
```

* 插件：TOC

```
npm i markdown-it-anchor
npm i @types/markdown-it-anchor -D  ## 支持 TypeScript
npm i markdown-it-toc-done-right  ## 支持 TypeScript
## 缺乏y @types/markdown-it-toc-done-right
```

### 9. 使用 Apollo React

```
npm i apollo-boost @apollo/react-hooks graphql
```

看 and 推荐的社区精选组件： https://ant.design/docs/react/recommendation-cn