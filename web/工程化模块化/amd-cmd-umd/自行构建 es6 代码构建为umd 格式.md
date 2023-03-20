[toc]

有时开源社区的 ES6 代码只提供 npm 包下载，但是我们有 `<script src=...` 的方式直接引用的需要，所以要自己打包 umd 格式。

那我们就以 `https://github.com/unshiftio/querystringify.git` 为例打包 umd

## 下载源代码

```bash
git clone https://github.com/unshiftio/querystringify.git
```

## 添加 webpack.config.js

进入 `querystringify` 目录，编辑 webpack.config.js 内容如下：

```javascript
module.exports = {
  entry: './index.js',
  output: {
    filename: './querystringify.min.js',
    // export to AMD, CommonJS, or window
    libraryTarget: 'umd',
    // the name exported to window
    library: 'qs'
  }
}
```

## 执行构建

```bash
webpack
```

将产生 querystringify.min.js 文件到 dist 目录

## 使用例

```html
<script src="querystringify.min.js" />
<script>
  qs.parse('foo=bar&bar=foo');  // { foo: 'bar', bar: 'foo' }
</script>
```