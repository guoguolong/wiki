一个NPM包没有TypeScript声明文件通常有两种方法补充：

## 使用方声明

如果你是该npm包的使用者，你没有权限修改npm包本身，那么可以在TS项目的根目录下，添加global.d.ts文件声明，这样ide工具如vs code就可以识别了（引入的代码没有红线）

假设包名为 `@koda/common`，如在CRA 创建的react项目中，可以在 react-app-env.d.ts文件声如下：

```typescript
declare module '@koda/common' {
    const constants: any;
    const helpers: any;
    export {
      constants,
      helpers
    }
  }
```



## 作者方声明

作者方声明具体也包含两种：

### 在NPM包内声明

在包的根目录下创建 index.d.ts 文件，内容如下：

```typescript
declare const _default: {
	constants: any;
	helpers: any;
};
export default _default;
```
如果文件名不是 index.d.ts 或者不在默认位置（根目录），需在 `package.json` 中声明 `types` 字段，假设声明文件在 `es/bundles.d.ts`，那么 `package.json` 声明如下：

```typescript
{
    ...
    "main": "./lib/main.js",
    "types": "./esm/bundles.d.ts"
}
```

### 发布声明到[@types organization](https://www.npmjs.com/~types) 

更多参考： https://www.typescriptlang.org/docs/handbook/declaration-files/publishing.html
