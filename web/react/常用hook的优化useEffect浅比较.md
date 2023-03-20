[toc]

先说说react原版的useEffect使用起来不便的地方

```javascript
useEffect(
  function() {
    // effect操作
  },
  ['a', 'b', { name: 'c' }]
);
```

这里的effect每次更新都会执行，因为第三个参数一直是不等的，{name: 'c'} !== {name: 'c'}，第二是在deps依赖很多的时候是真的麻烦，下面贴出改进版useEffect：

```javascript
import { useRef, useEffect } from 'react';
import _ from 'lodash';
export function useDeepCompareEffect<T>(fn, deps: T) {
  // 使用一个数字信号控制是否渲染，简化 react 的计算，也便于调试
  let renderRef = useRef<number | any>(0);
  let depsRef = useRef<T>(deps);
  if (!_.isEqual(deps, depsRef.current)) {
    renderRef.current++;
  }
  depsRef.current = deps;
  return useEffect(fn, [renderRef.current]);
}
```

在使用的时候什么都不用做，只需要把参数传进来就行。避免了之前的浅比较的缺陷，性能上有降低。但是deps稍微控制一下量，此处的性能不是大问题。

```javascript
useDeepCompareEffect(function() {
  // effect操作
}, 'a');
useDeepCompareEffect(
  function() {
    // effect操作
  },
  { name: 'c' }
);
useDeepCompareEffect(function() {
  // effect操作
}, 4567);
```