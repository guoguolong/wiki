## 安装 create-react-app 

```
yarn global add create-react-app 
```

## create-react-app 创建 typescript 工程骨架

```
create-react-app magic-mooc --template typescript
```

## 支持 webpack 配置定制

```bash
cd magic-mooc
yarn add webpack@4.44.2 --dev
yarn add react-app-rewired --dev
yarn add customize-cra --dev
```

关于兼容性： 因为 `react-app-rewired` 要求 `webpack 4.44.2`，所以在 package.json 中精确指定版本号：`"webpack": "4.44.2"`

更多参考 [React 定制webpack配置文件](React 定制webpack配置文件.html)

## 支持 less

```
yarn add less
yarn add --dev less-loader
```
less和less-loader版本兼容性问题： less-loader最新版本要求 `webpack 5`，由于前面使用了 `webpack 4.44.2`，所以需要明确安装 less@4 和 less-loader@6（该版本支持webpack 4），

更多参考 [React 定制webpack配置文件](React 定制webpack配置文件.html)

## 支持 redux

https://redux.js.org/ 基础版  
https://react-redux.js.org React集成版 (直接使用足矣)

```
yarn add redux
yarn add react-redux
yarn add --dev @types/react-redux  # support typescript
```

## 支持 redux 异步 dispatch

```
yarn add redux-thunk
yarn add @types/redux-thunk # support typescript

yarn add redux-saga
yarn add @types/redux-saga
```

## 支持 react-router

https://reacttraining.com/react-router/

react-router-dom  替代 react-router，直接安装可。

```
yarn add react-router-dom
yarn add --dev @types/react-router-dom 
```

## 远程调用：axios

```
yarn add axios
```

## 使用表单

```
yarn add formik
yarn add yup
```

## 使用 jest

create-react-app 已经集成了:
```
@testing-library/react
@testing-library/jest-dom --> @testing-library/dom
@testing-library/user-event
```

## 国际化

```shell
yarn add react-i18next
```

## AntD
```bash
yarn add @types/antd --dev
yarn add antd
```

## 支持 markdown

```
yarn add markdown-it
yarn add @types/markdown-it ## 支持 TypeScript

```

* 插件：TOC

```
yarn add markdown-it-anchor
yarn add @types/markdown-it-anchor  ## 支持 TypeScript

yarn add markdown-it-toc-done-right  ## 支持 TypeScript
## 缺乏yarn add @types/markdown-it-toc-done-right
```

## 使用 Apollo React

```
yarn add apollo-boost @apollo/react-hooks graphql
```
