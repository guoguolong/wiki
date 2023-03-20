[toc]
假设已经用 create-react-app 创建了一个 demo 项目，并安装了 `antd` 。

antd 采用 less，所以定制的前提是：要使 webpack 配置支持 less 先（参考 [React 定制webpack配置文件](http://note.youdao.com/noteshare?id=a57adfa9f74ffeacefa4b91a4238c917) ）。

首先，创建定制的 src/theme.less：

```less
@primary-color: #002200; // 全局主色
@link-color: #009900; // 链接色
```

## 方法 I

### 1. config-overides.js ：

```javascript
const { override, addLessLoader } = require("customize-cra");
const path = require('path')

const loaderOptions = {
    modifyVars: {
        // 'primary-color': '#900000',
        // 'link-color': '#00EE00',
        // 'border-radius-base': '2px',
        // or
        'hack': `true; @import "${path.join(__dirname, './src/theme.less')}";`
    },
    javascriptEnabled: true,
}
module.exports = override(
    addLessLoader(loaderOptions)
);
```

### 2. src/App.js 导入 less

```javascript
import 'antd/dist/antd.less';

// antd的组件
```

然后运行 `npm start` 试试。

## 方法 II：按需导入less

不想导入全量 `antd/dist/antd.less`，可以使用 babel-plugin-import 。

### 1. 安装 babel-plugin-import

```bash
yarn add babel-plugin-import
```

### 2. config-overides.js

使用 customize-cra 的 fixBabelImports 配置

```javascript
const { override, fixBabelImports, addLessLoader } = require("customize-cra");
const path = require('path')

const loaderOptions = {
    modifyVars: {
        'hack': `true; @import "${path.join(__dirname, './src/theme.less')}";`
    },
    javascriptEnabled: true,
}
module.exports = override(
    fixBabelImports('import', {
        libraryName: 'antd',
        libraryDirectory: 'es',
        style: true, // style 必须为 true 以支持 less，不可为 css
    }),
    addLessLoader(loaderOptions)
);
```

### 3. src/App.js 移除 `import 'antd/dist/antd.less';`

然后重新运行 `npm start` 试试。