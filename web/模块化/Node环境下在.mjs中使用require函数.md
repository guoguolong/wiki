## Node环境下在.mjs中使用require函数

本质上就是重新定义了 `require`函数：

```js
import { createRequire } from "module";
import dayjs from 'dayjs';
const require = createRequire(import.meta.url);
dayjs.extend(require('dayjs/plugin/duration'))

const r  = dayjs.duration(2, 'months').months();
console.log(r)
```

