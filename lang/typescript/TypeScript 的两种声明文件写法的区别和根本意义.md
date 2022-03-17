我们知道 NPM 包可以有内建的 TS 声明文件，从而免去使用 typings 工具安装 TS 声明文件的操作。那既然可以有内建的声明文件，为何还需要额外安装呢？因为不是 所有人都在使用 TypeScript，很多 NPM 模块都是纯 JavaScript 编写的，其作者 没有太大的可能性为之编写模块声明文件。而且内建的声明文件有一定约束。

TypeScript 的 DefinitelyTyped 声明文件有两种写法，一种叫做 **全局类型声明(Global Type Definition)**，另一个则是叫做 **模块导出声明(External Module Definition)**。

> External Module 一词源自 TypeScript 1.5 之前的 内部模块/外部模块 之说，而 1.5 之后内部模块变成了 namespace，外部模块直接化为模块， 不再有内外部模块之说。
>
> 所以这里将 `External Module Definition` 译为 `模块导出声明` 比较合适。

DefinitelyTyped 对此的说明是：

- 如果你的模块需要将新的名称引入全局命名空间，那么就应该使用全局声明。
- 如果你的模块无需将新的名称引入全局命名空间，那么就应该使用模块导出声明。

这两者有什么不同呢？

首先，最主要的区别在于，NPM 模块里面的内建声明文件必须使用模块导出声明写法， 否则 TypeScript 编译无法通过。下面来看一个简单的例子：

以一个名为 abc 的模块为例，TS源代码如下：

```ts
export function abc(s: string): string {

    return s.substr(0, 4);
}
```

其声明文件有两种写法：

- 第一种，模块导出声明写法

  ```ts
  declare interface funcAbcSign {
      (s: string): string
  }
  
  export declare let abc: funcAbcSign;
  ```

- 第二种，全局类型声明写法

  ```ts
  declare module "abc" {
      interface funcAbcSign {
          (s: string): string
      }
  
      export let abc: funcAbcSign;
  }
  ```

如果要理解两者的区别，首先要理解其意义。`模块导出声明写法` 中，单从这个文件内容 看来是无法得知这些内容属于哪个模块的。所以必须将之与模块放在一起，作为内建声明文件， TypeScript 编译器才能得知其所属的模块。（或者放进 typings 的 external 目录）

`全局类型声明写法` 中，实际上是将模块名称 `abc` 引入了全局空间，即告诉 TypeScript 编译器，存在一个叫 `abc` 的模块，想使用里面的名称，就 import 吧！

因为任何一个 Node.js 的模块都是必须依靠 require 加载的才能通过字段引用的方式 使用里面的名称，即是说 **一个模块无法真正地将任何名称引入全局的命名空间中，所以不应该也不能在一个模块 的内建声明文件里使用全局声明写法！**

这样，就明白了什么时候应该使用全局声明写法，什么时候应该使用模块导出声明写法了

如果你写的 TypeScript 文件是在浏览器上通过 `<script>` 标签加载的，那么里面的 变量、函数等都会被引入到全局命名空间中，这时候就需要为这个文件写一份全局类型声明 了！比如这个文件：

```ts
// 定义了一个全局变量，整个页面内都可见
let appStartedTime: Date = new Date();
```

你需要为之写一份 `.d.ts` 文件，供其它 ts 文件引用：

```ts
// 这里将变量名 appStartedTime 引入了 TypeScript 的全局命名空间中。
declare let appStartedTime: Date;
```

除此之外，你可以用一个全局类型声明文件定义多个模块、命名空间，例如：

```ts
// node.d.ts

declare namespace NodeJS {

    export interface Error {

        "name": string;
    }
}

declare module "http" {

    // Node built-in module http
}

declare module "fs" {

    // Node built-in module fs
}
```

或者深层模块声明：

```ts
declare module "sample/lib1" {

    export let name: number;
}

declare module "sample/lib2" {

    export let value: number;
}

declare module "sample" {

    export * from "sample/lib1";

    export * from "sample/lib2";
}
```

这是模块导出声明写法做不到的。

综上所述，最直观的解释是：

- 全局类型声明里的名称将被引入整个 TypeScript 全局命名空间中，从引用这个 声明文件起就可以自由使用。
- 模块导出声明里的名称必须通过 import/require 才能使用。



## 参考文档

https://www.typescriptlang.org/docs/handbook/namespaces-and-modules.html

