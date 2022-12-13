# Typedoc-给TypeScript项目写文档 

官网已经 https://typedoc.org/ 再清楚不过

```bash
# Install
npm install --save-dev typedoc

# Execute typedoc on your project
npx typedoc src/index.ts
```

 将会解析源代码文件里 export 的定义，生成`html`文件到  _docs 目录。

可以指定多个输入文件和目标生成目录

```bash
npx typedoc --entryPoints ../src/index.ts --out ./dist
```



## 使用配置文件

* `typedoc.json` : entryPoints可以指定多个入口文件

```json
{
    "$schema": "https://typedoc.org/schema.json",
    "entryPoints": ["../src/index.ts", "../src/module1.ts", "../src/module2.ts", "../src/module3.ts"],
    "out": "./dist"
}
```

```bash
npx typedoc --options ./typedoc.json
```

* `tsconfig.js`:  也可以把配置文件写在 `tsconfig.json` 中

```json
{
  // 省略常规 typescript 配置。。。

  // typedoc 配置
  "typedocOptions": {
    "entryPoints": ["../src/index.ts"],
    "out": "./dist"
  }
}
```

```bash
npx typedoc --tsconfig ./tsconfig.json
```

更多 https://typedoc.org/guides/options/

##  生成Markdown格式的文档

```bash
npm i -D typedoc-plugin-markdown
```

重新产生文档

```bash
npx typedoc --options ./typedoc.json
```

默认生成 markdown 文档，如果想仍然生产原始的 html文档，运行

```
npx typedoc --plugin none --options ./typedoc.json
```

## 文档写法

关于如何写 TS 注释，这不在本文的讨论范畴中，大家可以前往 [TypeDoc 官方文档](https:///typedoc.org/tags/alpha/) 或者 [tsdoc 标准](https://tsdoc.org/) 自行学习。
