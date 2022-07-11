# react中 useContext 和useReducer的使用

[TOC]

>useContext和useReducer 可以用来减少层级使用, useContext，可以理解为供货商提供一个公共的共享值，然后下面的消费者去接受共享值，只有一个供货商，而有多个消费者，可以达到共享的状态改变的目的。
>  useReducer 可以和 useContext 配合使用，useReducer 可以理解为所有的公共组件共享状态。有多个组件，但是都要共享同一个状态和改变状态后的值，这时候就需要公共的useReducer来改变了。
>  下面通过代码具体讲解如何使用useContext 和useReducer

## 一、useContext基本使用可以分为固定的三步

### 1. 根组件导入并调用createContext，创建Context对象

```jsx
import React, { useContext } from 'react'
const GlobalContext = React.createContext()
```

### 2. 在根组件中使用 Provider 组件包裹需要接收数据的后代组件，并通过 value 属性提供要共享的数据

```jsx
<GlobalContext.Provider value={
  {
    title: "12313",
    info: info,
    changeInfo: (value) => {
      setInfo(value)
    }
  }
}>
</GlobalContext.Provider>
```

注：数据在哪里保存？`info`，`changeInfo`对应根组件的一个state的读和写，这样就可以将共享数据放到组件的state里了。

### 3. 读去或修改共享数据的后代组件：

* 方法一：调用useContext`，返回Context对象；

```jsx
const ctx = useContext(GlobalContext)
return (
  <div onClick={() => { ctx.changeInfo({
      x: 1,
      y: 2,
    }); }}>
    {ctx.title}
  </div>
)
```

* 方法二：通过`GlobalContext.Consumer` 获得Context对象

```jsx
return (
  <GlobalContext.Consumer>
    {
      (ctx) =>
        <div className='business-product-details' >
        {ctx.info}
      </div>
    }
  </GlobalContext.Consumer>
)
```

### 4.完整例子

```jsx
import React, { useState, useContext } from 'react';

const GlobalContext = React.createContext({});

const ReducerTest = () => {
  const [list] = useState([
    { name: 'Andy', url: 'https://guoguolong.tech/2019/02/05/sanwang/sanwang.jpg', info: { 
        city: 'Istanbul', 
        hobby: 'Basketball',
        age: 5,
    } },
    { name: 'Alice', url: 'https://guoguolong.tech/2019/02/05/sanwang/sanwang.jpg', info: { 
        city: 'London', 
        hobby: 'Swimming',
        age: 1,
    } },
  ]);
  const [info, setInfo] = useState(null);

  return (
    <GlobalContext.Provider
      value={{
        title: 'Life Bomb',
        info: info,
        changeInfo: (value: any) => {
          setInfo(value);
        },
      }}
    >
      <div className="w-warp">
        <div className="business-why">
          <div className="business-ul" style={{display: 'flex'}}>
            {list.map((item, index) => (
              <PeopleSummary key={index} {...item} />
            ))}
          </div>
          <PeopleDetail />
        </div>
      </div>
    </GlobalContext.Provider>
  );
};

function PeopleSummary({ name, url, info }: any) {
  const ctx: any = useContext(GlobalContext);
  return (
    <div
      className="people"
      onClick={() => {
        ctx.changeInfo(info);
      }}
      style={{width: '300px'}}
    >
      <h2>{name}</h2>
      <div className="avatar">
        <img style={{width: '100%'}} src={url} alt={name} />
      </div>
    </div>
  );
}

function PeopleDetail() {
  return (
    <GlobalContext.Consumer>
      {(ctx: any) => <div className="people-detail">{ctx.info && JSON.stringify(ctx.info)}</div>}
    </GlobalContext.Consumer>
  );
}

export default ReducerTest;
```

## 二、useReducer基本使用可以分为固定的三步

### 1. 页面根组件定义reducer的函数和初始状态值intialState

reducer里面包含两个参数(prevState,action) 之前状态值和 操作改变的类型，定义初始状态值 intialState 可以是对象包含多个值

```jsx
const reducer=(prevState, action) => {
   console.log(prevState, acton)  // count:0 ,action.type:minus
}
const intialState = {
    count:0
}
```

### 2. 页面引入  useReducer  并且创建相关对象

```jsx
import React, { useReducer} from 'react'
const [state, dispatch] = useReducer(reducer, intialState)
```

### 3. 父组件定义相关的dispatch状态改变操作类型


```xml
<button onClick={() => {
   dispatch({
    type: "minus"
   })
 }}>-</button>
```

### 4. 完整例子

```jsx
import React, { useReducer } from 'react'
const reducer=(prevState,action) => {
    const newState = {...prevState}
    switch (action.type) {
        case 'minus':
            newState.count--;
            return newState ;
       case 'add':
        newState.count++;
            return newState  
        
        default: return newState
    }
}
const intialState = {
    count:0
}

export default function app() {
  const [state,dispatch] = useReducer(reducer,intialState)
  return ( 
      <div>
          <button onClick={() => {
              dispatch({
                  type: "minus"
              })
          }}>-</button>
          {state.count}
          <button onClick={() => {
              dispatch({
                type: "add"
            })
          }}>+</button>
       </div>
  )
}
```

这个一个简单的数字加加和减减，根据的是action的类型判断状态的改变。

## 三、useReducer结合useContext使用的例子

下面看一个例子是useReducer结合useContext使用的例子。如果共享状态值和共享改变后的状态值。

> 先看下完成后的页面效果：


![img](images/webp)其中两个按钮是组件1，child2 和child3 为组件2 和组件3 ，通过组件1的操作改变组件2和组件3的值共享之前的状态和改变之后的状态，具体demo如下



```jsx
import React, { useReducer, useContext } from 'react'
const intialState = {
    a: "1111",
    b: "22222"
}
const reducer = (prevState, action) => {
    let newState = { ...prevState }
    switch (action.type) {
        case "child2":
            newState.a = "aaaa";
            return newState;
        case "child3":
            newState.b = "bbbb";
            return newState;
        default: return newState;
    }

}
const GlobalContext = React.createContext()
export default function app() {
    const [state, dispatch] = useReducer(reducer, intialState)
    return (
        <GlobalContext.Provider value={
            {
                state: state,
                dispatch: dispatch
            }
        }>
            <div>
                <Child1></Child1>
                <Child2></Child2>
                <Child3></Child3>
            </div>
        </GlobalContext.Provider>)
}

function Child1() {
    const value = useContext(GlobalContext)
    return (<div>
        <button onClick={() => {
            value.dispatch({ type: "child2" })
        }}>改变child2</button>
        <button onClick={() => {
            value.dispatch({ type: "child3" })
        }}>改变child3</button>
    </div>
    )
}

function Child2() {
    const value = useContext(GlobalContext)
    return (
        <div style={{ background: "yellow" }}>
            Child2 {value.state.a}
        </div>
    )
}

function Child3() {
    const value = useContext(GlobalContext)
    return (
        <div style={{ background: "blue" }}>
            Child3  {value.state.b}
        </div>

    )
}
```