[toc]

Webpack MPA配置的本质是：entry、output、HtmlWebpackPlugin 3个配置。以 增加 front.html、admin.html 两个入口为例，Webpack 关键配置如下：

```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin');
....

{
    entry: {
        admin: './src/admin.tsx',
        front: './src/front.tsx'
    },
    output: {
      path: __dirname + '/public/static/bundle',
      filename: '[name].bundle.js',
    },
    plugins:[
        new HtmlWebpackPlugin({
            chunks: ['admin'],
            template: './public/admin.html',
            filename: 'admin.html',
        }),
        new HtmlWebpackPlugin({
            chunks: ['front'],
            template: './public/front.html',
            filename: 'front.html',
        })
    ...
    ]
}
```

其中 output 是可选的，关键点在于 entry 对象的 key 和 HtmlWebpackPlugin的 chunks一一匹配。

## 本文假设

* 使用官方 create-react-app 创建项目手脚架。
* 使用 react-app-rewired 、 customize-cra 定制 webpack 配置。

## 自定义 customize-cra 之 override 参数函数

### 定义 config-multi-pages.js 

* 依赖：react-dev-utils、html-webpack-plugin
* 要增加名字为 main 的 entry，否则 ManifestPlugin 会报告错误： Cannot read property 'filter' of undefined。这个错误如此隐晦，参考：https://github.com/timarney/react-app-rewired/issues/421
* 默认 HtmlWebpackPlugin 不能删除，应该修改为对应 main 入口对应的配置

```javascript
const updateHTMLWebpackPluginChunk = (plugins) => {
  plugins.filter(it => {
    const isThePlugin = (it.constructor && it.constructor.name && 'HtmlWebpackPlugin' === it.constructor.name)
    if (isThePlugin) {
      it.options.chunks = ['main']
    }
  });
};
...
```

完整源码： https://github.com/guoguolong/codes.web/blob/master/gist/config-multi-pages.js

### 调用 config-multi-pages.js（在config-override.js） 

```
const supportMultiPages = require('./config-multi-pages')
module.exports = {
    webpack: override(
        ...
        supportMultiPages(['front', 'admin']),
        ...
    )
    ...
}
```


## 第三方 customize-cra 之 override 参数函数包： react-app-rewire-multiple-entry

config-override.js 关键部分：

```
const multipleEntry = require('react-app-rewire-multiple-entry')([
    {
        entry: './src/front.tsx',
        template: 'public/front.html',
        outPath: '/front.html'
    },
    {
        entry: './src/admin.tsx',
        template: 'public/admin.html',
        outPath: '/admin.html'
    }
]);

module.exports = {
    webpack: override(
        ...
        multipleEntry.addMultiEntry
    )
    ...
}
```

官网 https://www.npmjs.com/package/react-app-rewire-multiple-entry 很详细，不赘述。