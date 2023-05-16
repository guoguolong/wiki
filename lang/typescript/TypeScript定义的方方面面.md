# app-ts 

在 ts 类型的应用中

## 1. 引用的库有TS定义 - 库里定义

库里如何ts定义，这里不做描述。下面示例如何使用库(`@guoguolong/lib-ts`)中的TS定义，例子如下：

```ts
import tsLib, { Pair } from '@guoguolong/lib-ts';

const channel: Pair = tsLib.constants.channel.WECHAT;
console.log('Channel: ', channel);
```

## 2. 引用库没有TS定义 - 应用里全局定义

**全局TS定义文件，不能使用`export/import`，只要定义文件中出现`export/import`就不被认为是全局文件。**

### 使用外部模块
前面的例子中，如果引用的库没有随带 ts 定义，仍然想使用上述方法写 TS，使用 `declare module`
为了做这个实验，将 `@guoguolong/lib-ts`的 `index.d.ts`代码注释，
把代码复制到当前项目根目录下的 `global.d.ts`，用`declare module '@guoguolong/lib-ts'`包裹

```ts
declare module '@guoguolong/lib-ts' {
  // 这里 @guoguolong/lib-ts 模块的 index.d.ts 内容
}
```

### 给 window 添加新的属性

* global.d.ts
```ts
interface Window {
  martCtx: string;
}
```

这样，.ts 文件里书写 `window.martCtx`不会报红。

### 添加一个全局变量声明

* global.d.ts
```ts
declare const globalPool;
```
这样，.ts 文件里书写 `console.log(globalPool)`不会报红。

### 3. 引用全局TS定义文件的方法

引用全局TS文件，有两种方法：

* 自动加载`.d.ts`（仅：`isolatedModules: false`）

`tsc`默认遍历项目目录及子目录下所有`.ts`文件和全局`.d.ts`文件，`.ts`通过`.d.ts`的类型检查后才转换为`.js`。注意，为了支持全局`.d.ts`加载，`tsconfig.json`必须设置`isolatedModules: false`（默认值）时，全局`.d.ts`才被允许自动加载。

**根据上述原则，前述`global.d.ts`文件名显然不是必须的，可以更名为`<anyname>.d.ts`，放在任何子目录都可被寻找到。**

另外，`tsconfig.json`可以指定 `include`配置，如：

```json
  "include": [
    "src",
  ],
```

限制`tsc`编译目录范围到`src`。


* 手动加载`.d.ts`（仅：`isolatedModules: true`）

如果 `tsconfig.json`中 `isolatedModules: true`，需要如下方法手动加载：

```ts
/// <reference path="./xxx.d.ts" />
```

>注：如果`.d.ts`移动到`src`目录下后，该目录下存在同名的`.ts`文件，必须在源代码文件顶部明确引入。
比如 `global.d.ts` 移动为 `src/index.d.ts`，由于该目录存在 `index.ts`，此时需要手动加载。

## FAQ

* `declare module "modulename" {}`和 `declare module modulename {}`
>后者 `declare module modulename {}` （不带引号）用作命名空间，现在弃用，用`declare namespace
 modulename {}`替换。

## TODO
* namespace 场景、用法

