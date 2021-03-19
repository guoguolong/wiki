## useMemo



```javascript
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

> 把“创建”函数和依赖项数组作为参数传入 useMemo，它仅会在某个依赖项改变时才重新计算 memoized 值。这种优化有助于避免在每次渲染时都进行高开销的计算。

也就是说useMemo可以让函数在某个依赖项改变的时候才运行，这可以避免很多不必要的开销。举个例子：

### 不使用useMemo



```jsx
function Example() {
    const [count, setCount] = useState(1);
    const [val, setValue] = useState('');
 
    function getNum() {
        return Array.from({length: count * 100}, (v, i) => i).reduce((a, b) => a+b)
    }
 
    return <div>
        <h4>总和：{getNum()}</h4>
        <div>
            <button onClick={() => setCount(count + 1)}>+1</button>
            <input value={val} onChange={event => setValue(event.target.value)}/>
        </div>
    </div>;
}
```

上面这个组件，维护了两个state，可以看到`getNum`的计算仅仅跟`count`有关，但是现在无论是`count`还是`val`变化，都会导致`getNum`重新计算，所以这里我们希望`val`修改的时候，不需要再次计算，这种情况下我们可以使用`useMemo`。

### 使用useMemo



```jsx
function Example() {
    const [count, setCount] = useState(1);
    const [val, setValue] = useState('');
 
    const getNum = useMemo(() => {
        return Array.from({length: count * 100}, (v, i) => i).reduce((a, b) => a+b)
    }, [count])
 
    return <div>
        <h4>总和：{getNum}</h4>
        <div>
            <button onClick={() => setCount(count + 1)}>+1</button>
            <input value={val} onChange={event => setValue(event.target.value)}/>
        </div>
    </div>;
}
```

使用`useMemo`后，并将`count`作为依赖值传递进去，此时仅当`count`变化时才会重新执行`getNum`。

## useCallback



```jsx
const memoizedCallback = useCallback(
  () => {
    doSomething(a, b);
  },
  [a, b],
);
```

> 把内联回调函数及依赖项数组作为参数传入 useCallback，它将返回该回调函数的 memoized 版本，该回调函数仅在某个依赖项改变时才会更新。当你把回调函数传递给经过优化的并使用引用相等性去避免非必要渲染（例如 shouldComponentUpdate）的子组件时，它将非常有用。

看起来似乎和useMemo差不多，我们来看看这两者有什么异同：

`useMemo`和`useCallback`接收的参数都是一样，都是在其依赖项发生变化后才执行，都是返回缓存的值，区别在于`useMemo`返回的是函数运行的结果，`useCallback`返回的是函数。

> useCallback(fn, deps) 相当于 useMemo(() => fn, deps)

### 使用场景

正如上面所说的，当你把回调函数传递给经过优化的并使用引用相等性去避免非必要渲染（例如 shouldComponentUpdate）的子组件时，它将非常有用。也就是说父组件传递一个函数给子组件的时候，由于父组件的更新会导致该函数重新生成从而传递给子组件的函数引用发生了变化，这就会导致子组件也会更新，而很多时候子组件的更新是没必要的，所以我们可以通过`useCallback`来缓存该函数，然后传递给子组件。举个例子：



```jsx
function Parent() {
    const [count, setCount] = useState(1);
    const [val, setValue] = useState('');
 
    const getNum = useCallback(() => {
        return Array.from({length: count * 100}, (v, i) => i).reduce((a, b) => a+b)
    }, [count])
 
    return <div>
        <Child getNum={getNum} />
        <div>
            <button onClick={() => setCount(count + 1)}>+1</button>
            <input value={val} onChange={event => setValue(event.target.value)}/>
        </div>
    </div>;
}

const Child = React.memo(function ({ getNum }: any) {
    return <h4>总和：{getNum()}</h4>
})
```

使用`useCallback`之后，仅当`count`发生变化时`Child`组件才会重新渲染，而`val`变化时，`Child`组件是不会重新渲染的。