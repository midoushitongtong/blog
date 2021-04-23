---
title: 可能不需要 redux 了, 但不该用 hook 去取代它
date: 2019-12-29 12:05:05
categories:
  - 开发杂记
---

### 前言

最近再补有关 [react hook](https://zh-hans.reactjs.org/docs/hooks-reference.html) 的文档，在看完基础 hook 的章节时 （useState, useEffect, useContext），可以大致明白 hook 的用途了，简单的来说，hook 可以让函数组件也能拥有自己的 `state`，并且可以使用 `componentDidMount`, `componentDidUpdate`, `shouldComponentUpdate` 等 class 组件里面才有的特性，后面看到额外 hook 的章节时，里面有一个名叫 `useReducer` 的 api，这让我对 hook 的用途又打上了一个新的问好，看完了 useReducer 的相关介绍以及用法后，就在想，这个叫 useReducer 的 api 会不会是用来取代 `redux` 的呢？于是乎就在网上查阅了一下相关资料，最后从网上得出的结论就是，hook 是可以用来取代 redux 的，但是不能用 hook 去 `完全取代` redux， 因为他们两者的出发点不同，解决问题的场景也有所不同。为了验证 hook 到底能不能完全取代 redux，我也做了相关的尝试，最终得出结论，如果想使用 hook 来完全取代 redux，还是有一些特定的需求是无法满足的，在这里记录一下。

<!--more-->

### 如何使用 hook 取代 redux

思路：

- 使用 useReducer 维护 state，这一步用于取代 redux
- 使用 createContext 让 state 进行提升，这一步用于取代 react-redux

具体步骤:

#### 创建名叫 `toDoStore.ts` 的文件，从文件名大概可以知道，这个文件是用来管理 state 的。

```tsx
import React, { useReducer, useContext } from 'react';

// state
interface ToDoState {
  count: number;
  list: Array<String>;
}

// action type
enum ToDoActionType {
  UPDATE_COUNT = 'updateCount',
  UPDATE_LIST = 'updateList',
}

// action
type ToDoAction =
  | {
      type: ToDoActionType.UPDATE_COUNT;
      count: number;
    }
  | {
      type: ToDoActionType.UPDATE_LIST;
      list: Array<String>;
    };

// reducer
const reducer = (state: ToDoState, action: ToDoAction): ToDoState => {
  switch (action.type) {
    case ToDoActionType.UPDATE_COUNT:
      return {
        ...state,
        count: action.count,
      };
    case ToDoActionType.UPDATE_LIST:
      return {
        ...state,
        list: action.list,
      };
    default:
      return state;
  }
};

// 初始化 state
const initToDoState: ToDoState = {
  count: 0,
  list: [],
};

// 初始化 context
const ToDoStoreContext = React.createContext<
  | {
      state: ToDoState;
      dispatch: React.Dispatch<ToDoAction>;
    }
  | undefined
>(undefined);

// contexct provider
const ToDoStoreContextProvider = ({
  children,
}: {
  children: React.ReactElement;
}): React.ReactElement => {
  const [state, dispatch] = useReducer(reducer, initToDoState);
  // 我们将 useReducer 这个钩子，绑定在 context 的 provider 中
  return <ToDoStoreContext.Provider value={{ state, dispatch }}>{children}</ToDoStoreContext.Provider>;
};

// use context
const useToDoContext = (): {
  state: ToDoState;
  dispatch: React.Dispatch<ToDoAction>;
} => {
  const toDoStoreContext = useContext(ToDoStoreContext);
  if (toDoStoreContext === undefined) {
    // 组件想使用 toDoContext 必须嵌套在 ToDoStoreContext.Provider 中(HOC)
    throw new Error('useToDoContext 必须在 ToDoStoreContext.Provider 中使用');
  }
  return toDoStoreContext;
};

export { ToDoStoreContextProvider, useToDoContext, ToDoActionType };
```

#### 创建一个或者多个组件，用于获取的 state，以及调用 dispatch 来改变的 state

```tsx
import React from 'react';
import { useToDoContext, ToDoActionType } from './toDoStore';

const ShowToDoState: React.FunctionComponent = () => {
  const { state } = useToDoContext();
  return (
    <section>
      <span>{state.count}</span>
      <ul>
        {state.list.map((listItem, index) => (
          <li key={index}>{listItem}</li>
        ))}
      </ul>
    </section>
  );
};

const EditToDoState: React.FunctionComponent = () => {
  const { state, dispatch } = useToDoContext();
  return (
    <section>
      <button
        type="button"
        onClick={() => dispatch({ type: ToDoActionType.UPDATE_COUNT, count: state.count + 1 })}
      >
        increment count
      </button>
      <button
        type="button"
        onClick={() =>
          dispatch({ type: ToDoActionType.UPDATE_LIST, list: [...state.list, 'hello world'] })
        }
      >
        add item
      </button>
    </section>
  );
};

const App: React.FunctionComponent = () => (
  <>
    <ShowToDoState />
    <EditToDoState />
  </>
);

export default App;
```

#### 将最顶部组件用 ToDoStoreContextProvider 进行包裹，这样子组件才能进行订阅 context 的变化

```tsx
import React from 'react';
import ReactDOM from 'react-dom';
import { ToDoStoreContextProvider } from './toDoStore';
import App from './App';

ReactDOM.render(
  <ToDoStoreContextProvider>
    <App />
  </ToDoStoreContextProvider>,
  document.getElementById('root')
);
```

demo:

<iframe height="450" width="740" src="https://codesandbox.io/embed/cant-replace-redux-with-hooks-fru24?fontsize=14&hidenavigation=1&theme=dark" frameborder="0" allowfullscreen>
</iframe>

### 总结

从上面使用 hook 取代 redux 的过程中，会发现有以下几点是 hook 无法满足的：

- useReducer 无法进行 combine，这意味着你将不能对 state 进行分组管理，如果你强行对 state 进行分组，那么你将创建多个 context，这无异于提高了整个应用的复杂度
- 并没有提供 react-redux 那样的 HOC（mapStateToProps mapDispatchToProps），这意味着你不能快速的查看组件到底使用了那些 state，需要 ctrl + f 来进行确认
- useContext 所带来的性能问题，一旦触发 dispatch，所有使用了 context 的组件都会进行重新的 render，虽然可以手动处理，但只要稍有不慎，可以说是前功尽弃

可能说的很不严谨, 经代表个人观点！
