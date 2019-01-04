---
description: React简单介绍
---

# 简单介绍

> 本文档示例代码使用语言为`typescript`

### 简介

react是Facebook的一个开源JavaScript类库。核心技术和概念为jsx，组件，v-dom，data flow。

#### jsx

react的模板代码，直接嵌入js代码中，文件后缀为`.jsx`或者`.tsx`

#### 组件

`state`和`props`是react组件的两个核心概念

**props**

```jsx
// 示例代码
import React, { Component } from 'react';
import { render } from 'react-dom';

const Helloworld = (props) => {
    return <div>{props.str}</div>;
}

// 加载组件到 DOM 
render(<Helloworld str='Hello world!' />, document);
```

`props`是在调用组件的时候传入不同的属性，组件内部无法改变自身的`props`，可以把`props`看作组件的配置属性。

**state**

`state`是组件的当前状态，可以把组件看作一个`状态机`，根据状态呈现不同的UI展示。一旦状态变更，组件就会自动调用`render`重新渲染UI，这个更改的动作会通过`this.setState`方法来触发。其他方式变更state是无法重新渲染的

#### v-dom

全称Virtual DOM，react实现的虚拟dom。组件dom结构映射在v-dom上，react在v-dom上实现了一个diff算法，当需要重新渲染组件的时候，会通过diff算法去寻找需要变更的组件，再把这个改动更新到浏览器的真实dom上。v-dom是一个纯粹的js数据结构，性能回避原生dom快很多

#### data flow

单向数据流是react推崇的一种应用架构方式，指只能从一个方向来修改状态，props的变更方式很好的解释了单向数据流

### JSX语法

jsx全称为JavaScript XML，是ECMAScript的类似XML的语法扩展。jsx为react开发的一套语法糖，也是react的基础

```jsx
// 示例代码
const helloworld = <div>Hello world!</div>; // 声明常量无状态组件，纯组件
const helloworldFunc = () => { // 声明无状态组件
    const str = 'Hello world!';
    return <div>{str}</div>;
}
interface Props{
    props1:string
}
interface State{
    state1:string
}
class DemoComponent extends React.Component<Props,State>{// class有状态组件
    render(){
        return <div>Hello world!</div>
    }
}
```

### 组件

react在es6中的组件在写法上分为class组件和function组件，在状态上分为有状态组件和无状态组件。

#### 写法区分

**class组件**

```jsx
// 示例代码
interface Props{
    props1:string
}
interface State{
    state1:string
}
class DemoComponent extends React.Component<Props,State>{
    render(){
        return <div>Hello world!</div>
    }
}
```

class组件包含state和完整的react组件生命周期,其中渲染函数为`render()`

**function组件**

```jsx
// 示例代码
interface Props{
    props1:string
}
const helloworldFunc = (props:Props) => {
    const str = 'Hello world!';
    return <div>{str}</div>;
}
```

function组件中，返回值即为渲染组件，在react16.7版本之前，function组件只能作为无状态组件使用

> 在react16.7版本中，react引入了react hooks，function组件也可以作为有状态组件来使用了

```jsx
// react hooks代码示例
import React,{useState} from 'react';
interface Props{
    props1:string
}
const hooksDemo = (props:Props) => {
    const [str,setStr] = useState('hello world!');
    // str即为state.str，setStr即为setState({str}),‘hello world!’为str的初始值
    return <div>{str}</div>;
}
```

#### 状态区分

**无状态组件**

无状态组件\(Stateless Component\)是最基础的组件形式，由于没有状态的影响所以就是纯静态展示的作用。一般来说，各种UI库里也是最开始会开发的组件类别。如按钮、标签、输入框等。它的基本组成结构就是属性（`props`）加上一个渲染函数（`render`）。由于不涉及到状态的更新，所以这种组件的复用性也最强

**有状态组件**

在无状态组件的基础上，如果组件内部包含状态（`state`）且状态随着事件或者外部的消息而发生改变的时候，这就构成了有状态组件（Stateful Component）。有状态组件通常会带有生命周期\(lifecycle\)，用以在不同的时刻触发状态的更新。这种组件也是通常在写业务逻辑中最经常使用到的，根据不同的业务场景组件的状态数量以及生命周期机制也不尽相同

