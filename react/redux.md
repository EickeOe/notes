# Redux

## Redux

### 什么是 Redux？

官方解释：redux 是 js 应用的可预测状态的容器。 可以理解为全局数据状态管理工具（状态管理机），用来做组件通信等。

\## 为什么使用 Redux？

下面的图是不使用 Redux 和使用 Redux 时 父子组件之间的通信方式 ![](https://oscimg.oschina.net/oscnet/90a9e69d3675d26d70dd270708d442188b3.jpg)

可以看到，没用使用 Redux 的情况，如果两个互不相干的组件之间的需要通信的话，可能需要多个中间组件为他们进行消息传递，这样浪费资源，代码也会变得复杂。

Redux 中以单一数据源 Store 来存储状态数据，所有的状态都是可预测的（也就是说，所有的状态都是我们自己定义的）

### 设计思想

* web 应用是一个状态机，视图与状态是一一对应的。
* 所有的状态，保存在一个对象里面。

### 概念

#### state

redux 把每一个状态`state`（数据）都看作一个对象，这里的计数器，我们作为初始值，例如计数器的状态：

```typescript
const initHomeState = { num: 0 };
```

这个对象就像一个“模型”，除了没有 setter。这样代码的不同部分不能任意改变状态，导致难以重现的错误。

#### action

redux 将每一个更改动作描述为一个`action`，要更改`state`中的内容，你需要发送`action`。一个`action`是一个简单的对象，用来描述`state`发生了什么变更，例如计数器的`action`分为递增和递减，代码如下：

```typescript
// 递增
const incrementAction = { type: COUNTER_INCREMENT };
// 递减
const decrementAction = { type: COUNTER_DECREMENT };
```

如果状态发生了变化，我们就知道它为什么会改变。

#### reducer

我们需要将`state`和`action`联系起来，为此我们写了一个叫`reducer`的函数。`reducer`将`state`和`action`作为参数，并返回应用的一个新的`state`，例如计数器的`reducer`：

```typescript
const counter = (
  state: { num: number } = initHomeState,
  action: { type: string }
) => {
  switch (action.type) {
    case COUNTER_INCREMENT:
      return Object.assign({}, state, { num: ++state.num });
    case COUNTER_DECREMENT:
      return Object.assign({}, state, { num: --state.num });
    default:
      return state;
  }
};
```

接下来,我们需要写一个`rootReducer`来管理应用的完整状态,需要使用`combineReducers`来结合`reducer`

```typescript
import { combineReducers } from "redux";
​
const rootReducer = combineReducers({
  counter
});
```

#### store

`store`就是 redux 保存状态`state`（数据）的地方,可以看作一个容器,整个应用中只能有一个`store`,创建 store 需要传递`reducer`,通常会传递`rootReducer`

```typescript
import { createStore } from "redux";
const store = createStore(rootReducer);
```

#### store.dispatch()

`store.dispatch()`是组件发出`action`的唯一方法。

`dispatch`发出的`action`会传递到`reducer`中去，通过`reducer`返回下一个`state`。

```typescript
store.dispatch(incrementAction);
store.dispatch(decrementAction);
```

简单来说就是组件通过使用`dispatch`发送`action`到`reducer`来改变`state`从而改变组件的`props`,更新`vdom`，最后更新真实`dom`。

## redux 和 react native 组件的连接

我们需要安装 redux,react-redux:

```bash
yarn add redux react-redux @types/react-redux @types/redux
```

### react-redux 的 API

#### connect()

connect()用来将 react 组件连接到 redux 的`store`，就是使 react 组件可以接收到`store`中的数据。

**connect()的参数**

1. `mapStateToProps?: Function`
2. `mapDispatchToProps?: Function | Object`
3. `mergeProps?: Function`
4. `options?: Object`

**mapStateToProps?: (state, ownProps?) => Object**

这个方法是用来指定 react 组件将使用哪些`store`中的数据，也就是说，当`store`中的`state`更新后，这个方法会接收`state`（方法的第一个参数），将`state`中变更的值通过**返回值**传递给 react 组件的`props`。例如计数器的`mapStateToProps`：

```typescript
const mapStateToProps = (state: any) => {
  return {
    num: state.counter.num
  };
};
```

**mapDispatchToProps?: Object | (dispatch, ownProps?) => Object**

这个方法的第一个参数就是`store.dispatch`,可以定义发送`action`的方法，并且将方法通过**返回值**传递给 react 组件的`props`。例如计数器的`mapDispatchToProps`：

```typescript
const mapDispatchToProps = (dispatch: any) => ({
  increment: () => dispatch(incrementAction),
  decrement: () => dispatch(decrementAction)
});
```

**mergeProps?: (stateProps, dispatchProps, ownProps) => Object**

这个方法用来生成 react 组件最终的`props`。一般情况下很少使用。

**options?: Object**

同样很少使用，可以点[这里](https://react-redux.js.org/api/connect#mergeprops-stateprops-dispatchprops-ownprops-object)来查看官方文档

### 使用

然后需要为计数器按钮组件`Counter`建立连接

```jsx
let Counter = ({ increment, decrement, num }) => (
  <View style={{ flex: 1 }}>
    <View style={{ flex: 1, textAlign: "center", paddingTop: "50%" }}>
      <Text style={{ fontSize: 30, textAlign: "center" }}>{num}</Text>
    </View>
    <View style={{ flex: 2 }}>
      <Button onPress={increment}>递增</Button>
      <Button onPress={decrement}>递减</Button>
    </View>
  </View>
);
const mapStateToProps = (state: any) => {
  return {
    num: state.counter.num
  };
};
const mapDispatchToProps = (dispatch: any) => ({
  increment: () => dispatch(incrementAction),
  decrement: () => dispatch(decrementAction)
});
Counter = connect(
  mapStateToProps,
  mapDispatchToProps
)(Counter);
```

在`react native`的根组件（通常是 App 组件，App.tsx）使用`react-redux`提供的`Provider`组件设置全局`store`：

```jsx
import React, { Component } from "react";
import { Provider } from "react-redux";
import { connect } from "react-redux";
​
export default class App extends Component<Props> {
  render() {
    return (
      // 设置全局store
      <Provider store={store}>
        <Counter />
      </Provider>
    );
  }
}
```

接下来就可以点击`递增`按钮，来始`store.counter.num`进行递增了，递减也是可以的。
