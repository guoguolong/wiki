## StrictMode

StrictMode 在开发模式下重复调用以下函数或方法以确保没有副作用。原文如下：

Strict mode can’t automatically detect side effects for you, but it can help you spot them by making them a little more deterministic. This is done by intentionally double-invoking the following functions:

- Class component constructor, render, and shouldComponentUpdate methods
- Class component static getDerivedStateFromProps method
- Function component bodies
- State updater functions (the first argument to setState)
- Functions passed to useState, useMemo, or useReducer

https://reactjs.org/docs/strict-mode.html#identifying-unsafe-lifecycles

## Reactjs生命周期速记图

http://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/

## Redux中间件redux-thunk ：支持异步

* context 不太熟悉
* Hook中的: Hook API 索引 没有完整学习
  - 我们给 Hook 设定的目标是尽早覆盖 class 的所有使用场景。目前暂时还没有对应不常用的 getSnapshotBeforeUpdate，getDerivedStateFromError 和 componentDidCatch 生命周期的 Hook 等价写法，但我们计划尽早把它们加进来
* React中异步函数在render中怎么调用？
* test部分（test-utils等）用法未学习   
    https://testing-library.com/docs/react-testing-library/intro

* React.memo 和 shouldComponentUpdate 、PureComponent使用还不熟悉
* 如果 state 的逻辑开始变得复杂，我们推荐 用 reducer 来管理它，或使用自定义 Hook。
* webpack 配置文件怎么弄？
* useContext/ createContext 咋用？
* 使用 css、postCSS、SASS
* 