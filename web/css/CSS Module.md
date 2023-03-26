[toc]

## CSS Module 目的

**解决 CSS 中全局作用域的问题**

CSS Modules 不是官方规范或浏览器中的实现，而是构建步骤中的一个过程（在 Webpack 或 Browserify 的帮助下），它改变了类名和选择器的作用域（即有点像命名空间）。

官方github： https://github.com/css-modules/css-modules

Ruanyifeng Blog: https://www.ruanyifeng.com/blog/2016/06/css_modules.html

## 项目开启 CSS Module

CRA 和 vite 都默认支持了CSS Module. 

### `webpack`如何想支持 CSS Module，设置如下：

```javascript
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/i,
        loader: "css-loader",
        options: {
          modules: true,
          localIdentName: "[name]_[local]__[hash:base64:5]"
        }
      }
    ]
  }
};
```
localIdentName 可以定义生产的哈希类名，默认是 [hash:base64]，详件 [css-loader](https://webpack.docschina.org/loaders/css-loader/)。`localIdentName`选项的占位符有：

```
[name] 源文件名称 (样式文件的文件名)
[folder] 文件夹相对于 compiler.context 或者 modules.localIdentContext 配置项的相对路径。
[path] 源文件相对于 compiler.context 或者 modules.localIdentContext 配置项的相对路径。
[file] - 文件名和路径。
[ext] - 文件拓展名。
[hash] - 字符串的哈希值。基于 localIdentHashSalt、localIdentHashFunction、localIdentHashDigest、localIdentHashDigestLength、localIdentContext、resourcePath 和 exportName 生成。
[<hashFunction>:hash:<hashDigest>:<hashDigestLength>] - 带有哈希设置的哈希。
[local] - 原始类名。
```

## 如何使用 CSS Module

### 1. 创建 item.module.less

经过适当配置，**less/scss/css** 都是可以支持 CSS Module的，下为less 一例：

```less
.item {
  background-color: #f8f8f8;
  border-radius: 10rem;
  .item-body {
    color: #999;
    font-size: 14rem;
  }
}
```

### 2. React组件中引用 item.module.less

```jsx
import styles from './item.module.css'
export default () => {
  return <div className={styles.item}>
       <div className={styles['item-body']}>
          空气炸锅
       </div>
  </div>
}
```

编译后生成的 css 类名被混淆了

```html
<div class="_device-item_5lt3a_7">
    <div class="_device-body_5lt3a_31">
        空气炸锅
    </div>
</div>
```

## 设置作用域 :global/:local

如果想使部分 css类名保持原样，便于使用方进行override，使用 `:global`。

* item.module.less修改如下：‘

```diff
- .item {
+ :global(.item) {
  background-color: #f8f8f8;
  border-radius: 10rem;
  .item-body {
    color: #999;
    font-size: 14rem;
  }
}
```

* Item.jsx 修改

```diff
import styles from './item.module.css'
export default () => {
-  return <div className={styles.item}>
+  return <div className="item">
  ...
  </div>
}
```

同理，和`:global`相比，:local`是默认行为。

## 更多请参考 Ruanyifeng博客

https://www.ruanyifeng.com/blog/2016/06/css_modules.html
