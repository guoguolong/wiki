# React.memo、useMemo和useCallback 通俗解释版

## I. useMemo - 避免不必要的函数执行

**问题场景**：下面这个 Example 组件，每次执行 `n++`或者`m++`的状态变化时， `calcNum` 函数都会执行。但是`calcNum`的运行结果不依赖 `n`，仅仅依赖 `m`，最好是在仅`m`变化时运行`calcNum`，那应用性能就提高了。

```jsx
import { useState } from 'react';

const Example = () => {
  const [m, setM] = useState(1);
  const [n, setN] = useState(1);

  // calcNum 可能是个复杂的计算函数
  const calcNum = () => {
    console.log('calcNum executed.')
    return Array.from({ length: m * 100 }, (v, i) => i).reduce((a, b) => a + b);
  }

  return (
    <div>
      <h4>总和：{calcNum()}</h4>
      <div>
        <button onClick={() => setM(m + 1)}>update m {m}</button>
      </div>
      <div>
        <button onClick={() => setN(n + 1)}>update n {n}</button>
      </div>
    </div>
  );
};

export default Example;
```

### 使用useMemo

如果想`calcNum`仅在`m`状态值改变时运行，此时使用 `useMemo`.

```jsx
import { useState, useMemo } from 'react';

const Example = () => {
  const [m, setM] = useState(1);
  const [n, setN] = useState(1);

  // calcNum 可能是个复杂的计算函数
  const calcNum = useMemo(()=> {
    console.log('calcNum executed.')
    return Array.from({ length: m * 100 }, (v, i) => i).reduce((a, b) => a + b);
  }, [m]);

  return (
    <div>
      <h4>总和：{calcNum}</h4>
      <div>
        <button onClick={() => setM(m + 1)}>update m {m}</button>
      </div>
      <div>
        <button onClick={() => setN(n + 1)}>update n {n}</button>
      </div>
    </div>
  );
};

export default Example;

```

使用`useMemo`后，并将`m`作为依赖值传递进去，此时仅当`m`变化时才会重新执行`calcNum`。

### 可以用useEffect 来替代 useMemo

将上述函数计算写到 useEffect里，依赖`m`变化，然后将结算结果写入到一个新的 state变量 `num`里。

```javascript
import { useState, useEffect } from 'react';

const Example = () => {
  const [m, setM] = useState(1);
  const [n, setN] = useState(1);
  const [num, setNum] = useState(0);

  useEffect(() => {
    console.log('Executed calculation')
    setNum(Array.from({ length: m * 100 }, (v, i) => i).reduce((a, b) => a + b));
  }, [m]);

  return (
    <div>
      <h4>总和：{num}</h4>
      <div>
        <button onClick={() => setM(m + 1)}>update m {m}</button>
      </div>
      <div>
        <button onClick={() => setN(n + 1)}>update n {n}</button>
      </div>
    </div>
  );
};

export default Example;
```
## II. React.memo: 避免子组件不必要的重新渲染

**问题场景**：一个 Example 组件里有m和n数据。还有一个Child子组件，它接受一个`m`作为它的外部数据。当点击把`update n`的按钮时，我们知道，Example 会再次执行，那么Child子组件也会再次执行。

```javascript
const Example () => {
  const [m, setM] = React.useState(1);
  const [n, setN] = React.useState(1);
  const onClickM = () => {
    setN(m + 1);
  };
  const onClickN = () => {
    setN(n + 1);
  };

  return (
    <div className="example">
      <div><button onClick={onClickN}>update n {n}</button></div>
      <div><button onClick={onClickM}>update m {n}</button></div>
      <Child data={m}/>
    </div>
  );
}

function Child(props) {
  console.log("child 执行了");
  console.log('假设这里有大量代码')
  return <div>child: {props.data}</div>;
}
```

通过在Child函数组件里打log，我们发现，改变n，Child也会执行一次。可是明明Child这个组件只依赖m，n变化它为什么要跟着执行呢？怎么解决这个问题？

就可以使用 `React.memo` 封装Child

```javascript
const Child2 = React.memo(Child);
```

把Child放在 `React.memo` 里，得到一个Child2，Child2是Child优化之后的函数。在 `Example` 里渲染这个Child2

结果：改变n，Child不执行了，只有改变它依赖的外部数据m时，才会执行。

使代码更简洁：直接把匿名函数组件放在memo里作为参数，得到Child组件。

```javascript
const Child = React.memo((props)=>{
  console.log("child 执行了");
  console.log('假设这里有大量代码')
  return <div>child: {props.data}</div>;
});
```

### 小结

React默认会有多余的render，当改变组件里的数据时，由于组件本身会再次执行，就导致这个组件的子组件也会再次执行。

`React.memo` 使得一个组件**只有在它依赖的props变化时，才会执行**。



## III. useCallback - 避免子组件因为函数引用变化重复渲染

**问题场景**: 如果子组件接受了一个`函数props`，这个函数在父组件里定义，给子组件传进去。当我改变父组件里的n时，Example()肯定会再次执行。就会**导致里边`onCalcTotal`函数定义代码块重新执行**，每次执行`onCalcTotal`都是新的引用，因此，React.memo 判断Child 组件属性发生了变化，将重新渲染 Child 组件。

```javascript
import React, { useCallback, useMemo, useState } from 'react';

const Parent = () => {
  const [m, setM] = useState(1);
  const [n, setN] = useState(1);
  const onClickM = () => {
    setM(m + 1);
  };
  const onClickN = () => {
    setN(n + 1);
  };

  const onCalcTotal = () => {
    console.log('Calc total.')
  }; // 再次执行时，虽然函数内容一样，但是地址不同了

  return (
    <div className="example">
      <div>
        <button onClick={onClickM}>update m {m}</button>
      </div>
      <div>
        <button onClick={onClickN}>update n {n}</button>
      </div>
      <Child data={m} onCalcTotal={onCalcTotal} />
    </div>
  );
};

const Child = React.memo((props: any) => {
  console.log('child 执行了');
  return <div onClick={props.onCalcTotal}>child: {props.data}</div>;
});

export default Parent;

```

解决方法就是使用 `useCallback`，将 `onCalcTotal` 修改如下：

```javascript
import { useCallback } from 'react';

const onCalcTotal = useCallback(() => {
  console.log('Calc total.')
}, [m]); // 再次执行时，虽然函数内容一样，但是地址不同了
...
```

