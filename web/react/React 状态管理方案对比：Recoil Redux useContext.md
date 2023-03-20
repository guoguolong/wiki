[toc]

# React 状态管理方案对比：Recoil Redux useContext.md

状态管理是前端开发者绕不开的问题，为了解决这个问题，出现了一些优秀的状态管理方案，`Redux`、`Recoil`、还有 React 自带的`useContext`+`useReducer`，可选的方案太多，以至于会眼花缭乱，这一次我将使用这三个方案完成同一个状态管理需求，供大家参考。

ps：`Recoil`大家可能比较陌生，也是 Facebook 的亲儿子

## 状态区分

我的理解，状态其实就是数据的管理，强烈建议大家把接口数据和本地数据分开管理。现实中我们往`Redux`存的绝大多数数据其实来自于接口，这是一个很不好的做法。建议使用[React Query](https://react-query.tanstack.com/)做接口状态管理，用上面的方案做本地状态管理，`React Query`这里不深入展开了。

## 状态管理需求

需求很简单，有 home，aubut 两个页面，这两个页面分别有一个计数器，我们要保留计数器的状态。

## 在线预览

[在线预览](https://codesandbox.io/s/angry-http-frr8m?file=/src/App.tsx)

不愿看代码同学可以直接滑到最后看对比

## Redux

Redux 的用法大家比较熟悉，首先创建`homeSlice.ts`和`aboutSlice.ts`,然后在`store`注册,再通过自己包装过的`useDispatch`和 `useSelector`使用。

```ts
// ./store/homeSlice.ts
import { createSlice, PayloadAction } from "@reduxjs/toolkit";

const homeSlice = createSlice({
  name: "home",
  initialState: {
    value: 0,
  },
  reducers: {
    setCount: (state, action: PayloadAction<number>) => {
      state.value = action.payload;
    },
  },
});
export const { setCount } = homeSlice.actions;

export default homeSlice;
```

`aboutSlice.ts`和`homeSlice.ts`十分相似,就不贴代码了。

```ts
// ./redux/store/index.ts
import { configureStore } from "@reduxjs/toolkit";
import aboutSlice from "./aboutSlice";
import homeSlice from "./homeSlice";

const store = configureStore({
  reducer: {
    home: homeSlice.reducer,
    about: aboutSlice.reducer,
  },
});

export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;

export default store;
// ./redux/store/hook.ts
import { TypedUseSelectorHook, useDispatch, useSelector } from "react-redux";
import type { RootState, AppDispatch } from ".";

export const useAppDispatch = () => useDispatch<AppDispatch>();
export const useAppSelector: TypedUseSelectorHook<RootState> = useSelector;
```

redux 先天不足，需要自己封装一层以获得类型检查

```ts
// ./redux/Home.tsx
import { useAppDispatch, useAppSelector } from "./store/hooks";
import { setCount } from "./store/homeSlice";

const Home = () => {
  const count = useAppSelector((state) => state.home.value);
  const dispatch = useAppDispatch();

  return (
    <div>
      <p>is Home page : {count}</p>
      <button
        onClick={() => {
          dispatch(setCount(count + 1));
        }}
      >
        add
      </button>
    </div>
  );
};
export default Home;
```

## useContext + useReducer

> 如果以下例子不好理解，可参考`react中 useContext 和useReducer的使用`

这个方案的思路为在上层创建需要共享的 context，然后恭喜，通过 useReducer，修改数据

```ts
// ./store/HomeContext.ts
import { createContext, Dispatch } from "react";

interface State {
  count: number;
}
export interface InitContextProps {
  state: State;
  setState: Dispatch<Partial<State>>;
}
export const dataInit = {
  count: 0,
};
export const reducer = (
  prevState: State,
  updatedProperty: Partial<State>
): State => ({
  ...prevState,
  ...updatedProperty,
});
const HomeContext = createContext<InitContextProps>({
  state: dataInit,
  setState: () => {
    throw new Error("HomeContext:超出 HomeContext.Provider 范围");
  },
});
export default HomeContext;
```

创建 HomeContext

```ts
//./context/index.tsx
import React, { useReducer } from "react";
import HomeContext, {
  dataInit as homeDataInit,
  reducer as HomeReducer,
} from "./store/HomeContext";
import AubotContext, {
  dataInit as aubotDataInit,
  reducer as AubotReducer,
} from "./store/AubotContext";
const Context = () => {
  const [homeState, setHomeState] = useReducer(HomeReducer, homeDataInit);
  const [aubotState, setAubotState] = useReducer(AubotReducer, aubotDataInit);
  return (
    <>
      <HomeContext.Provider
        value={{ state: homeState, setState: setHomeState }}
      >
        <AubotContext.Provider
          value={{ state: aubotState, setState: setAubotState }}
        >
          ...
        </AubotContext.Provider>
      </HomeContext.Provider>
    </>
  );
};
export default Context;
```

在上层包裹

```text
//./context/Home.tsx
import React, { useContext } from "react";
import HomeContext from "./store/HomeContext";
const Home = () => {
  const { state, setState } = useContext(HomeContext);
  return (
    <div>
      <p>is Home page : {state.count}</p>
      <button
        onClick={() => {
          setState({ count: state.count + 1 });
        }}
      >
        add
      </button>
    </div>
  );
};
export default Home;
```

## Recoil

```ts
// ./recoil/store.ts
import { atom } from "recoil";

export const homeState = atom({
  key: "homeState",
  default: 0,
});
// ./recoil/index.ts
import React from "react";
import { RecoilRoot } from "recoil";

const RecoilPage = () => {
  return (
    <>
      <RecoilRoot>...</RecoilRoot>
    </>
  );
};
export default RecoilPage;
```

只需在上层包裹一层`RecoilRoot`就可以了，建议在根结点添加。

```text
// ./recoil/home.ts
import React from 'react'
import { useRecoilState } from 'recoil';
import { homeState } from './store';

const Home = () => {
  const [count, setCount] = useRecoilState(homeState);

  return (<div>
    <p>is Home page : {count}</p>
    <button onClick={() => {
      setCount(count + 1)
    }} >add</button>
  </div>)
}
export default Home
```

## 对比

### Reudx

- TS支持较弱
- 需要在`store`注册，这本不是缺点，但是`recoil`不需要，就怕比较。
- 状态是统一管理，多人协作时，容易文件冲突。
- 完成这个需求所需的代码最多

### useContext + useReducer

- 根正苗红，React自带，无需第三方库
- 每新创建一个Context，需要在上层包裹，也就会需要频繁更改与主逻辑无关的上层文件。
- TS支持好，但是想要好的TS支持，模版语法有些多。

### recoil

- TS支持好
- 无需注册，只需在顶层包裹RecoilRoot就够了，后期几乎不会更改
- 使用最简单，同时代码量也最少

## 我的推荐

`recoil`>`useContext + useReducer`>`Reudx`>其他