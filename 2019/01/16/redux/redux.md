---
title: redux基础使用
date: 2019-01-16 11:53:05
tags: redux
categories: redux
description: Redux 是 JavaScript 状态容器，提供可预测化的状态管理。可以让你构建一致化的应用，运行于不同的环境（客户端、服务器、原生应用），并且易于测试。
---

Redux 是 JavaScript 状态容器，提供可预测化的状态管理。

可以让你构建一致化的应用，运行于不同的环境（客户端、服务器、原生应用），并且易于测试。不仅于此，它还提供 超爽的开发体验，比如有一个时间旅行调试器可以编辑后实时预览。

Redux 除了和 React 一起用外，还支持其它界面库。 

redux的三大原则：
- 单一数据源
- state的只读性
- 纯函数执行修改

经常写vue的话，一定会了解到vuex这个数据管理工具，而redux也是和vuex类似的，也是一个数据管理工具，不同的是，它不依赖ui框架，除了react外，还支持其他的ui库。

下面就以一个加减计算及求和的应用介绍一下redux的简单使用

# action
>`Action` 是把数据从应用（这里之所以不叫 view 是因为这些数据有可能是服务器响应，用户输入或其它非 view 的数据 ）传到 store 的有效载荷。它是 `store` 数据的唯一来源。一般来说你会通过 `store.dispatch()` 将 `action` 传到`store`。

一般在定义action时，都会先定义一个actionType来方便对action的管理

```js
//actionType.js
export const INCREMENT = 'increment';

export const DECREMENT = 'decrement';

```

Redux 中每个 action 构造函数都返回一个 action 对象

>我们约定，`action` 内必须使用一个字符串类型的 `type` 字段来表示将要执行的动作。多数情况下，`type` 会被定义成字符串常量。当应用规模越来越大时，建议使用单独的模块或文件来存放 `action`(如上)。

```js
//action.js
import * as ActionTypes from './ActionTypes.js';

export const increment = (counter) => {
  return {
    type: ActionTypes.INCREMENT,
    counter: counter
  };
};

export const decrement = (counter) => {
  return {
    type: ActionTypes.DECREMENT,
    counter: counter
  };
};
```
# Reducer

>`Reducers` 指定了应用状态的变化如何响应 `actions` 并发送到 `store` 的，记住 `actions` 只是描述了有事情发生了这一事实，并没有描述应用如何更新 `state`。

>`reducer` 就是一个纯函数，接收旧的 `state` 和 `action`，返回新的 `state`。`reducers`中并不会直接修改`state`中的数据，而只会返回新的数据，对于不修改的数据则原封不动的移入

```js
//reducers.js
import * as ActionTypes from '. /ActionTypes.js';
export default (state , action) => {
const {counter} = action;  //action中定义的对象
switch (action.type) {
    case ActionTypes.INCREMENT : //根据action中的type来决定如何更新数据
        return {...state, [counter]:state[counter] + 1};
    case ActionTypes.DECREMENT :
        return {...state , [counter]:state[counter] - 1);
    default:
        return state
    }
}
```

# store

>Store 就是把action和reducers联系到一起的对象。Store 有以下职责：
 - 维持应用的 state；
 - 提供 getState() 方法获取 state；
 - 提供 dispatch(action) 方法更新 state；
 - 通过 subscribe(listener) 注册监听器;
 - 通过 subscribe(listener) 返回的函数注销监听器。 
 再次强调一下 Redux 应用只有一个单一的 store。当需要拆分数据处理逻辑时，你应该使用 reducer 组合 而不是创建多个 store。

这边用到了 Redux 库提供的 `createStore` 函数，这个函数第一个参数代表更新状态的 `reducer`，第二个参数是状态的初始值，第三个参数可选，代表 `Store Enhancer`,在这边暂时用不到

 ```js
 //store.js
import {createStore} from 'redux'
import reducer from './reducers.js';
const initValues = {
    'Frist': 0,
    'Second': 10,
    'Third': 20
}
const store= createStore (reducer , initValues) ;
export default store ;
 ```

 # 使用

 state通过getState()方法来获取,获取我们需要的数据之后，初始化本地的state数据，

 数据变化则通过`store.dispatch()`来提交对应动作，通知`redux`更新数据，为了保持`redux`数据和本地`state`数据保持一致，我们需要监听`redux`的数据变化，来同步更新本地的`state`数据,监听`redux`数据变化通过`store.subscribe()`方法，该方法接受一个回调，数据变化后触发回调，回调中我们加入本地数据的更新。

 ```js
 import store from '.. /Store.js'

class Counter extends Component {
    constructor(props) {
        super(props);
        this.state= this.getOwnState();
        getOwnState() {
            return {
                value: store.getState()[this.props.caption] //父组件传入对应的数据key
                    }
        }
    }


    onChange () {//数据更新
        this.setState(this.getOwnState());
    }
    componentDidMount() { //监听数据变化
        store.subscribe(this.onChange);
    }
    componentWillUnmount() {//卸载监听
        store.unsubscribe(this.onChange)
    }
    onIncrement() {//提交增加动作
        store.dispatch(Actions.increment(this.props.caption));
    }
    onDecrement() {//提交减少动作
    store.dispatch(Actions.decrement(this.props.caption ));
    }
}

 ```

 同理，父组件中的计算求和也是先从redux中初始化数据，然后监听变化来更新数据

 ```js

import React, { Component } from 'react';

import store from '../Store.js';

class Summary extends Component {
  constructor(props) {
    super(props);

    this.onChange = this.onChange.bind(this);

    this.state = this.getOwnState();
  }

  onChange() {
    this.setState(this.getOwnState());
  }

  getOwnState() {
    const state = store.getState();
    let sum = 0;
    for (const key in state) {
      if (state.hasOwnProperty(key)) {
        sum += state[key];
      }
    }
    return { sum: sum };
  }
  componentDidMount() {
    store.subscribe(this.onChange);
  }
  componentWillUnmount() {
    store.unsubscribe(this.onChange);
  }
}
 ```
 # 总结

 redux的理念来自于flux，强调的是“单向数据流”的管理方式，所有的数据都统一管理，所有的数据变化都需要提交dispatcher，无法直接修改数据，防止的数据的紊乱，不统一导致的问题。