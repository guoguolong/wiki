[toc]
说“打包的umd可以用import导入”，这句话不准确。

## npm安装umd.js时，并使用babel插件支持import
如果打包的umd.js文件被npm方式安装，需要设置babel插件 [ @babel/plugin-transform-modules-umd](https://babeljs.io/docs/en/next/babel-plugin-transform-modules-umd.html)
```
npm install --save-dev @babel/plugin-transform-modules-umd
```
然后在babel.config.js添加这个plugin
```
module.exports = {
  ...
  plugins: ['@babel/plugin-transform-modules-umd'], //添加这行
};
```

该插件将umd.js转成es6模块，这才使得import可以工作。
如果你使用vue-cli3+, bable.config.js已经预设如下：
```
module.exports = {
  presets: [
    '@vue/cli-plugin-babel/preset'
  ]
}
```
已经暗含了 @babel/plugin-transform-modules-umd 插件的设置。

## 文件方式引用 umd.js 不可以用import

```
import hello from '../lib/xxx.umd.js';
```

这样调用是不会工作的。