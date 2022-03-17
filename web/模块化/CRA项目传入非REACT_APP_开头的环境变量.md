# CRA项目使用非 REACT_APP_开头的环境变量的方法



cra项目现只能传入 REACT_APP_ 开头的环境变量到项目代码里，否则可以借助 webpack 的 DefinePlugin 插件

我们直接以 `react-app-rewire` 方式组织的Web项目为例:

##  config-override.js 

```js
const webpack = require('webpack');
....

module.exports = {
  webpack: override(
    ...
    (config, env) => {
      config.plugins.push(
        new webpack.DefinePlugin({
          'process.env.TARO_ENV': JSON.stringify(process.env.TARO_ENV),
        })
      );
      return config;
    }
  }
  ...
}
```



## pacakge.json（运行脚本）

```json
{
  "scripts": {
    "start": "TARO_ENV=h5 REACT_APP_VER=uat react-app-rewired start",
    ...
  }
}
```



## 项目代码，如  My.tsx

```tsx
export default () => {
  console.log('process.env.TARO_ENV:::', process.env.TARO_ENV)

  return (
    <div>
      My Sample
    </div>
  );
}
```

