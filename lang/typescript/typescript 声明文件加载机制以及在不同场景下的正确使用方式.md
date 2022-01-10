# typescript 声明文件加载机制以及在不同场景下的正确使用方式

## .d.ts 文件是 typescript 的声明文件，主要用来给编辑器做代码提示用，具体的书写位置和方式根据你的具体需求而定。

**（嫌弃太长可以直接跳到使用方式的 1.1 和 2.3）**

很多初学者（比如我）刚开始接触 ts 时，一直分不清 .ts 和 .d.ts 的区别，不知道 .d.ts 存在的意义是什么。刚开始跟着各种教程搭建好了 ts 开发环境，写好了 hello world 时，发现就算没有写 .d.ts 文件，编辑器（这里以及之后的编辑器均指宇宙第一编辑器 vscode ）也可以获得代码提示，甚至就算写的不是 ts 而是 js，只要 import 的依赖关系明确，对于一些简单的函数依然能获得最基本的入参、返回值的形参提示。从而就想知道 .d.ts 文件存在的意义是什么。

其实这个问题稍微思考一下就能知道。假设我们用 ts 开发了一个 npm 库，经过编译打包之后发布到了 npm 上，其他用户下载了我们这个库，下载到他本地的一般是一个 dist/index.js ，package.json 里的 main 指向这个 dist/index.js 文件。这时候不管这个用户开发使用的是 ts 还是 js，当他 import 我们这个库的时候都无法获得代码提示。

**.d.ts 文件主要是 for 第三方库，让第三方库的使用者可以获得良好的代码和接口提示**。本文主要介绍 .d.ts 文件的加载机制以及在**纯 js**开发环境中如何使用 .d.ts 声明文件，获得代码提示和接口声明。

## 加载机制

### 一些定义：

- 三斜线指令

- - 定义： `/// <reference path="xxx.d.ts"/>` 或者 `/// <reference types="xxx"/>`

  - 特点：

  - - 在 .ts 中已经不再使用，但是在 .d.ts 中还是有一定用处
    - 只能出现在文件的最开头，并且前面只能有注释或者别的三斜线指令
    - 有点类似 C++ 的 #include，但tsc 不会把 xxx 的代码插入替换到三斜线指令的位置

- 声明文件：

- - 定义：.d.ts 后缀的文件

  - 特点：

  - - 里面不允许有任何函数的实现
    - 顶层作用域里只能出现 `declare``import``export``interface``三斜线指令`

- 全局类声明文件：

- - 定义：如果一个声明文件的**顶层作用域**中没有 `import` && `export`，那么这个声明文件就是一个全局类声明文件
  - 特点：**如果一个全局类声明文件在 ts 处理范围内，** 那么全局类声明文件中的 declare 会在全局生效

- 模块类声明文件：

- - 定义：如果一个声明文件的**顶层作用域**中有 `import` || `export`，那么这个声明文件就是一个模块类声明文件
  - 特点：里面的 declare 不会在全局生效，需要按模块的方式导出来才能生效

### 一些行为：

- ts 编译器会包含下面的所有 .d.ts 文件：

- - tsconfig.json 的 `file （并集） include （差集） exclude`
  - 对于 node_modules/@types 下的每个 npm 包，ts 会按照 node 解析包的那一套流程（如果有 package.json并且里面有 main 字段，将 main 字段的文件作为入口文件。如果上面流程失败，将 index.d.ts 作为入口文件。否则抛出错误）。这个入口文件里面的三斜线指令、import 所引入的其他文件，会按照**其他文件自己的规则生效**



```js
// node_modules/@types/my/index.d.ts

/// <reference path="a.d.ts"/>
import "./b"; 
import c from "./c";  
declare const index = 1; 
export default index;
```

```js
// node_modules/@types/my/a.d.ts

declare const a = 1;
```

```js
// node_modules/@types/my/b.d.ts

declare const b = 1;
```

```js
// node_modules/@types/my/c.d.ts

declare const c = 1;

export default c;
```

上面的代码的效果为:

- 全局：a、b
- 非全局： index、c

index 文件中，由于含有 import export，所以为模块类声明文件，里面使用 declare 声明的 index 为非全局，在被使用时只有 `import index from 'my';`时才能拿到 index。而引入的 a、b 文件，这两个文件虽然是被 index 这个模块类声明文件引入的，但是这两个文件自己本身是全局类声明文件（既没有 import 也没有 export），所以这两个文件里面 declare 的变量都可以在全局访问到。而引入的 c 文件里面含有 export，所以为模块文件，里面的 c 无法在全局被访问。

**FAQ | Tips | 注意事项：**

1. 既然 a、b 文件为全局类声明文件，那么为什么还要在 index 中引入？

因为一般情况下 tsconfig.json 的 exclude 会加入 node_modules，所以理论上 node_modules 里面的所有文件都不会被 ts 编译。而 node_modules/@types 比较特别，里面的每个包会被作为一个模块，这个模块只会有一个入口文件（比如默认的 index.d.ts）。这个入口文件中没有引入的，都不会被 ts 处理。所以上面的代码在 index 文件中去掉【import "./b";】 之后，b 文件中的【declare const b = 1;】不会被 ts 看到（a 同理）。

2. import 和 /// <reference /> 的区别是什么？

主要还是历史遗留问题，三斜线指令出现的时候 ES6 还没出来。三斜线指令不会将一个全局文件变成模块文件，而 import 会。如果你需要一个在一个全局文件 b 里用另一个文件 c 里的变量，就可以用三斜线指令，因为用 import 会把 b 变成一个模块文件。

3. 我想在一个模块文件里导出全局变量怎么办？

这种情况只能导出一个 namespace：

```js
export const d = 1;     
export as namespace whateverthisis; // 全局 namespace 名，whateverthisis.d 就可以访问到
```

（export as namespace 只能在模块文件里面使用）

4. vscode 的 ts 代码提示的缓存机制

vscode 的 ts 代码提示会缓存 node_modules/@types 下的每个 package 的入口文件地址（package.json 的 main 字段）。如果手动去改变了某个包的 main 字段，改成了另一个文件，那么在重启 ts server（vs code自带的一个东西），修改是不会生效的，入口文件依旧是以前的入口文件（但是去修改入口文件里的内容是可以生效的)

5. declare module A 和 declare module 'a' 的区别

前者已经被废弃，使用 declare namespace A代替；后者用于扩展一个已有的模块 a。全局文件下的 declare module 'xx' 会在全局环境生成一个名为 xx 的模块，并且可以在里面定义这个 xx 模块应该有的导出，一般用来添加或补充 node_modules 中的模块的声明文件。同名的 declare module 里面的导出会合并。

## 使用方式

常用的是下面的 **1.1** 、**2.3**

### 1. 添加全局的代码提示（直接输入变量即可获得代码提示）

### 1.1 添加自定义全局变量

### 场景：全局注入变量，比如小程序的 Page

**正确操作**：

```js
//global.d.ts （简化版）

interface Opt<D> {
  data: D, 
}  

declare function Page<D>(opt:Opt<D>):void;
```

### 1.2 添加第三方库的全局代码提示

### 场景：添加 jquery、lodash 等全局变量

在简单 web 页面的场景下，经常是直接新建一个 index.html，在里面用 script 标签引入 jquery lodash 的外部 CDN。然后新建一个 index.js，在 index.html 中用 script 标签引入 index.js，这样在 index.js 里是可以直接使用 $ _ 这两个变量的，但是输入 $ _ 时无法获得代码提示。 (image)

如果获得了代码提示，大概率是 ts 的缓存目录生效了：~/Library/Caches/typescript/3.8 里面可能有曾经下载过的 npm 包。不过一个正常的 ts 项目一般根目录会有 tsconfig.json ，里面的 include 里可以规定需要 ts 编译的目录，这个字段填写好了之后 ts 就不会去缓存目录里找了，除非你把缓存目录也写进去。

**正确操作**： `npm i @types/jquery @types/lodash -D`

这样会得到 node_modules/@types/jquery 和 node_modules/@types/lodash 两个目录。ts 会检查 node_modules/@types 下面的所有 .d.ts 文件，所以编辑器就获得了 `jQuery $ _` 三个全局变量的代码提示。

### 2. 添加局部的模块代码提示

### 2.1 扩展挂载到全局的模块的代码提示

**场景1：给 Array.prototype 加一个方法**

虽然这种行为不被推荐，但是某些情况下你可能的确需要这么做。比如现在给 Array.prototype 加了一个 getSum 方法，获取数组中所有元素的和：

```js
// somewhere_else.js

Array.prototype.getSum = function(){    
  return this.reduce((result, value) => result + value, 0); 
}
```

**正确操作**：

```js
// global.d.ts

interface Array<T> {   
  getSum(): T extends number ? number : void; 
}
```

**场景2：给 jQuery 加一个静态方法 $.getHelloWorld**

通过简单分析（opt+click）可以知道，暴露在全局的 jQuery 和 $ 本身是两个全局的 const 常量，类型是 JQueryStatic，所以只需要给这个 JQueryStatic 接口增加 getHelloWorld 方法就可以了。

**正确操作**：

```js
// global.d.ts

interface JQueryStatic {   
  getHelloWorld(): string; 
}
```

**场景3：给 lodash 加一个静态方法 _.getHelloWorld**

通过简单分析（opt+click）可以知道，暴露在全局的 _ 本身是一个 const 变量，类型为 _.LoDashStatic，但是这个 _.LoDashStatic 并没有被暴露到全局，所以需要使用 `declare module` 的语法来 override lodash 这个模块

**正确操作**：

```js
// module.d.ts （和 global.d.ts 分开，否则会使 global.d.ts 中的 declare 失去全局性）

import _ from 'lodash'; // 注意这个 import 必须写在 declare module 外部 

declare module 'lodash' {
  interface LoDashStatic {
    getHelloWorld(): string;   
  }  
}
```

### 2.2 扩展非全局模块，但自带声明文件的模块（esm、commonjs）

**场景：node 的 fs 增加一个 getHelloWorld 方法**

先通过`npm i @types/node -D`安装 node 的声明文件

**正确操作**：

```js
// global.d.ts

declare module 'fs' {   
  export function getHelloWorld(): string; 
}
```

### 2.3 扩展非全局模块，并且不自带声明文件的模块

**场景1：你从 npm 上面下载了一个 ex-module 模块，但是这个作者很懒，没有提供声明文件，@types 社区也没有人提供，你想自己给 ex-module 写声明文件，便于后续开发**

假设 ex-module 长这样:

```js
// node_modules/ex-module/index.js

import _ from 'lodash';  

export function add(a, b) { return a + b; } 
export function minus(a, b) { return a - b; } 
export default function getLodash() { return _; }
```

**正确操作**：

```js
// global.d.ts

declare module 'ex-module' {   
  import { LoDashStatic } from 'lodash'; // 注意这个 import 必须写在 declare module 内部 
  export function add(): number;   
  export function minus(): number;   
  export default function getLodash(): LoDashStatic; 
}
```



```js
// index.js

import getLodash,{ add, minus } from 'ex-module';

add(1, 130);
```


坑点：

- 由于 js + ts 模块化过于混乱，各自的实现也有冲突，建议全部严格按照 ES6 的方式来写
- 比如上面的 global.ts 中，如果删掉 export default 宇航，在 index.js 中 `import ex from 'ex-module'`，输入 ex 时候， vscode 会提示 ex.add 是一个函数，但实际上 ex 是 undefined

**场景2：你用 js 给自己写了一个 util 库，里面有各种各样的工具函数，想给他们加上声明文件**

```js
// util/math.js

export function add(a, b) {  
  return a + b; 
}  

export function minus(a, b) {   
  return a - b; 
}
```

**正确操作**：

```js
// util/math.d.ts

export function add(a: number, b: number): number; 
export function minus(a: number, b: number): number;
```

### 总结： ts 由于各种复杂的历史遗留问题，模块方面比较混乱，坑也很多，还是建议统一用 ES6 模块