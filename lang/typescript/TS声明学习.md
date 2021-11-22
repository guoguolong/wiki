## CRA项目中为window对象扩展类型

为window增加属性 `context`， src目录下的 `react-app-env.d.ts` 内容如下：

 ```typescript
 /// <reference types="react-scripts" />
 export {};
 declare global {
   interface Window {
     context: any;
   }
 }
 ```

这样 vsc 中 输入 `window.` 就可以给出属性提示了.

## 再增加一个全局函数 `__t`

在前面基础上增加一行

```typescript
/// <reference types="react-scripts" />
export {};
declare global {
  interface Window {
    context: any;
  }
  function  __t(s: string):string;    
}
```

## NPM包类型带命名空间 namespace

方案I

```typescript
import Person from 'path'

export interface A {
  person: Person
}

export as namespace Koda

```

方案2

```typescript
import Person from 'path'

declare namespace Test {
  interface A {
    person: Person
  }
}

export = Koda
export as namespace Koda
```

通常可以让 `namespace` 与 包名同名

