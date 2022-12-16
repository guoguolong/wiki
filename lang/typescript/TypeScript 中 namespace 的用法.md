# TypeScript 中namespace的用法

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
