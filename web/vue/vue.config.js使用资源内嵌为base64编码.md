```javascript
module.exports = {
  filenameHashing: true,
  productionSourceMap: true,
  chainWebpack: config => {
    config.module.rule('images').use('url-loader').loader('url-loader').tap(options => {
      return {
        limit: 50000,
        esModule: false
       };
    })
  }
}
```

chaianWeback 的 config.module 的配置等同于：

```javascript
module: {
  rules: [
    {
      test: /\.(png|svg|jpe?g|gif)$/,
      use: [
        {
          loader: 'url-loader',
          options: {
            limit: 50000,
            esModule: false
          },
        }
      ]
    }
  ]
}
```