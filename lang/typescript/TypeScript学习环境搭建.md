### 基础准备

先创建一个工程目录进入，安装依赖：`typescript` 和 `ts-node`（运行 ts的工具）

```bash
pnpm i typescript
pnpm add -g ts-node
```



### 2. 初始化 tsconfig.js

```
pnpm exec tsc --init
```

写一个ts例子文件 hello.ts

```typescript
function hello(event: string) {
  const queue = new Map();
  queue.set(event, ['a','b']);
  queue.get(event)!.push('c');
  console.log(`Event [${event}]:`, queue.get(event))
}

hello('error')
```

### 3. tsc 编译 .ts 然后运行之

* 转译

```
tsc
```

直接运行 `tsc`会编译当前目录及子目录下的.ts文件（使用`tsconfig.js`配置），但是直接 `tsc  <文件名>`则不会使用 `tsconfig.js`。

```
tsc hello.ts
```

* 运行

```shell
node hello.js
```

### 4. 用ts-node直接运行 .ts

```
ts-node hello.ts
```


