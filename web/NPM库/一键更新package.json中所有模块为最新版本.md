设 3 个源代码文件，在同一个目录，分别是：`api.ts`、`helper.ts`、`client.ts`



## api.ts

```ts
export function transfer() {
  console.log('ran transfer');
}

export function obj2csv() {
  console.log('ran obj2csv');
}

export default  {
  aObj2csv: obj2csv,
  aTransfer: transfer
}
```



## helper.ts

```ts
export * from "./api";
export { default as Api } from "./api";
// export {} // 无用的行
```

## client.js

```ts
import { obj2csv, Api } from './helper'

obj2csv(); // 输出：ran obj2csv
Api.aTransfer();  // 输出： ran tansfer
```



