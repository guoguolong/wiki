## 1. 修改 src/config/webpack.base.ts：新增图片资源的rule

module.rules增加节点：

```javascript
{
    test: /\.(png|svg|jpe?g|gif)$/,
    use: [
        {
            loader: 'url-loader',
            options: {
                limit: 500000,
                esModule: false
            },
        }
    ]
},
```

然后安装 url-loader

> npm i url-loader -S

## 2. 修改 src/commands/build.ts的compileDir方法

```javascript
return compileFile(filePath);
```

修改为：

```javascript
if(!filePath.match(/\.(jpe?g|png|gif|svg)$/)) {
    return compileFile(filePath);
}
```

## 3. 修改 src/compiler/compile-sfc.ts 中的compileTemplate方法

```javascript
function compileTemplate(template: string) {
  const result = compileUtils.compileTemplate({
    compiler,
    source: template,
    isProduction: true,
  } as any);

  return result.code;
}
```

修改为：

```javascript
function compileTemplate(template: string) {
  const result = compileUtils.compileTemplate({
    compiler,
    source: template,
    isProduction: true,
    transformAssetUrls: {
        audio: 'src',
        video: ['src', 'poster'],
        source: 'src',
        img: 'src',
        image: ['xlink:href', 'href'],
        use: ['xlink:href', 'href']
    }
  } as any);

  return result.code;
}
```