* 父组件渲染的JSX tree中，在某个位置使用的 <Child /> 对象(react element，即React.createElement()返回的对象)与上次渲染时相同（在同一个位置使用同一个对象引用），这时Child组件不会re-render。

* Child组件被React.memo装饰，并且传给Child组件的props与上次渲染相同。这种情况可以理解为React.memo帮我们缓存了上次渲染时使用的<Child/>jsx element对象。即：

```javascript
const Component = React.memo(({count}) => {
    console.log('Component re-render')
    return <div>Count: {count}</div>
})
```

等价于

```js
const Component = ({count}) => {
    return React.useMemo(() => {
        console.log('Component re-render')
        return <div>Count: {count}</div>
    }, [count]) // deps数组包含所有Parent可能传入的props value
}
```

看 codes.demo/react/src/speed-optimizer/Parent.js