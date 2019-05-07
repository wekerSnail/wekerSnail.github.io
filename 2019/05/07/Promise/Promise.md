---
title: Promise的基础使用
date: 2019-05-07 10:02:54
tags: js
categories: js
description: Redux 是 JavaScript 状态容器，提供可预测化的状态管理。可以让你构建一致化的应用，运行于不同的环境（客户端、服务器、原生应用），并且易于测试。
---

`Promise` 是异步编程的一种解决方案：
从语法上讲，`promise`是一个对象，从它可以获取异步操作的消息；从本意上讲，它是承诺，承诺它过一段时间会给你一个结果。
`promise`有三种状态：`pending`(等待态)，`fulfiled`(成功态)，`rejected`(失败态)；状态一旦改变，就不会再变。创造`promise`实例后，它会立即执行。


`promise`的使用可以解决异步的回调地狱问题，不在需要层层的回调嵌套，让代码变得复杂难读。

> Promise是一个构造函数，自己身上有all、reject、resolve这几个眼熟的方法，原型上有then、catch等同样很眼熟的方法。

```js
let p = new Promise((resolve, reject) => {
    //异步操作
    setTimeout(() => {
        console.log('执行完成');
        resolve('我是成功！！');
    }, 2000);
});

```
以上就是创建了一个简单的`Promise`函数，他接受一个回调函数，并且传入两个参数`resolve`和`reject`。这两个参数是两个回调函数，`resolve`代表成功会后的回调，`reject`代表失败后的回调。`Promise`创建完就会立即执行，例如以上代码运行完就会输出 *执行完成*

## then的用法

`then`方法可以接受两个回调函数作为参数，一个是`resolve`状态的回调函数，一个是`reject`状态的回调函数。

```js
p.then((success)=>{
    console.log(success)
},(fail)=>{
    console.log(fail)
})
```
两个函数的传入值都是接收的`Promise`中的传出值。

#### then的链式调用
`then`方法返回的是一个新的`promise`,所以`then`可以链式调用

```js
p.then((data) => {
    console.log(1);
    return 2
})
.then((data) => {
    console.log(data);
})
.then((data) => {
    console.log(3);
});
```
运行以上代码会依次数出，*1,2,3*,其中第一个`then`中的传入值接受自`Promise`的传出值，而后面一个`then`则接受收上一个的return值，第三个则无返回值,所以后面两个的`data`值为`2`和`undefined`


## reject的用法

`reject`是`rejected`状态的回调函数，调用的是`then`中第二个参数的回调函数。

```js
let p = new Promise((resolve, reject) => {
    //异步操作
    setTimeout(() => {
        console.log('执行完成');
        reject('我是失败！！');
    }, 2000);
});

```

## catch的用法

`catch`是用来接受`reject`的回调函数的，和`then`的第二个回调函数是一样的效果，同时`catch`还可以捕获`Promise`中的代码报错，所以一般`then`都是用来接受成功回调，而在最后统一使用`catch`来处理错误或者异常。


## all的用法

`Promise.all`方法用于将多个 `Promise` 实例，包装成一个新的 `Promise` 实例。
`Promise`的`all`方法提供了并行执行异步操作的能力，并且在所有异步操作执行完后才执行回调。


```js
let Promise1 = new Promise(function (resolve, reject) { resolve(1) })
let Promise2 = new Promise(function (resolve, reject) {
    setTimeout(() => {
        resolve(2)
    }, 2000)
})
let Promise3 = new Promise(function (resolve, reject) { resolve(3) })

let p = Promise.all([Promise1, Promise2, Promise3])

p.then((a) => {
    console.log(a)
}, () => {

})
```

运行上面代码，两秒之后输出[1,2,3],`all`方法会等所有`Promise`都成功之后在运行回调，同时接受的传入参数是以上所有`Promise`的返回值的数组，顺序和传入`Promise`一致，如果中间有一个`promise`失败，则`all`方法就会运行失败的回调。

## race的用法

`race`和`all`方法类似，也是处理多个`Promise`的，但是作用是相反的，`race`同样接受一个`promise`的数组，但是`race`的回调是第一个`Promise`状态改变就运行，而不会等所有`Promise`状态都改变。接受的参数则是该状态改变的`promise`传出的参数值。

## async和await的使用

`async`可以将异步函数转化成同步函数来执行，结合`Promise`可以实现异步的同步并发控制

```js
function promiseAction() {
    return new Promise(function (resolve, reject) {
        setTimeout(() => {
            resolve(2)
        }, 2000)
    })
}
async function p() {
    let a = await promiseAction()
    console.log(a)
}

p()
```
以上函数，在运行`p`函数时，首先会调用`promiseAction`函数，该函数是一个异步函数，所以在函数之前加上`await`前缀之后，异步就会变成同步运行，下面的代码会等该异步成功之后才会运行，所以最后输出*2*


**以上说的都是Promise基础使用的一些总结，具体的使用还需要结合使用场景进行一定的封装**

> [参考资料:es6入门](https://es6.ruanyifeng.com/#docs/promise)

> [参考资料:掘金](https://juejin.im/post/5afe6d3bf265da0b9e654c4b#heading-6)