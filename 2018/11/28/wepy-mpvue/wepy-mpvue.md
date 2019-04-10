---
title: wepy-mpvue对比
date: 2018-11-28 15:44:35
tags: 小程序
categories: 小程序
toc: true
description: mpvue和wepy两个小程序开发框架的区别对比
---


# wepy对比mpvue

## 小程序配置部分

wepy可以配置小程序开发者工具的设置项 ，mpvue没有相关配置

```json 
{
//project.config.json文件
"description": "project description",
  "setting": {
    "urlCheck": true,
    "es6": false,
    "postcss": false,
    "minified": false
  },
  "compileType": "miniprogram",
  "appid": "touristappid",
  "projectname": "Project name",
  "miniprogramRoot": "./dist"
}
```

wepy全局配置和页面配置都集成进了页面单文件内,mpvue需要单独的main.json配置文件

```js
export default class Index extends wepy.page {
	config = {
		window: {
      backgroundTextStyle: 'light',
      navigationBarBackgroundColor: '#fff',
      navigationBarTitleText: 'WeChat',
      navigationBarTextStyle: 'black'
   		 },
	}
}
```

## 组件化

wepy和mpvue都支持组件化，写法和使用和vue的写法基本一致，区别在于

mpvue的使用和vue完全一样，wepy稍有不同，wepy同页面使用相同组件是，需要不同的声明

**mpvue不支持slot组件，wepy支持**

```html
// index.wpy
//使用两个Counter组件时，components中需要区别声明再来使用，mpvue是声明一次对此重复使用
<template>
    <view>
        <panel>
            <h1 slot="title"></h1>
        </panel>
        <counter1 :num="myNum"></counter1>
        <counter2 :num.sync="syncNum"></counter2>
        <list :item="items"></list>
    </view>
</template>

<script>
import wepy from 'wepy';
//引入List、Panel和Counter组件
import List from '../components/list';
import Panel from '../components/panel';
import Counter from '../components/counter';

export default class Index extends wepy.page {
    //页面配置
    config = {
        "navigationBarTitleText": "test"
    };

    //声明页面中将要使用到的组件
    components = {
        panel: Panel,
        counter1: Counter,
        counter2: Counter,
        list: List
    };
```



## 文件结构

```js
mpvue一个页面由三个文件构成
main.json,index.vue,mian.js
```

```js
wepy一个单文件 xxx.wpy
配置也写在js部分，参看上文
```



## 对于原生api的影响

- mpvue对于原生api没有任何影响，直接和原生的使用方式一样

- wepy对于原生api做了优化处理，通过配置，可以使用promise封装的api，对并发处理更优,wepy中的`wx.xx`接口都改为`wepy.xx`

  ```js
  import wepy from 'wepy';
  
  async onLoad() {//使用promise方式的api，支持es7的异步管理
      await wepy.login();
      this.userInfo = await wepy.getUserInfo();
  }
  //promise方式的请求
  wepy.request('xxxx').then((d) => console.log(d));
  ```



## wxml部分

- mpvue支持wxml的所有标签，但是数据绑定方式是按照vue方式来，事件绑定方式也是按照vue方式
- wepy同样支持所有的wxml标签，数据绑定方式和原生一样，事件绑定和原生略有不同，[参看文档](https://tencent.github.io/wepy/document.html#/?id=%E7%BB%84%E4%BB%B6%E8%87%AA%E5%AE%9A%E4%B9%89%E4%BA%8B%E4%BB%B6%E5%A4%84%E7%90%86%E5%87%BD%E6%95%B0)



## wxss部分

两者基本相同，都支持预处理器



## js部分

- mpvue基本和vue相同，生命周期兼容vue和小程序两者生命周期

- wepy写法类vue，结构类react，生命周期遵从小程序规范

  ```js
  <script>
      import wepy from 'wepy'
      import Child from '../components/child'
  
      export default class Index extends wepy.page {
          components = {
              child: Child
          }
  
          methods = {//此处和vue的methods不同，此处的methods仅是dom事件，自定义事件直接写在class下面
              parentFn (num, evt) {
                  console.log('parent received emit event, number is: ' + num)
              }
          }
      }
  </script>
  
  ```


## 数据绑定

两者都是数据动态绑定，直接赋值即可`this.title = 'this is title';`

不同之处：wepy在异步的数据跟新时需要手动刷新一下this.$apply()

```js
setTimeout(() => {
    this.title = 'this is title';
    this.$apply();
}, 3000);
```



## 数据管理

- mpvue使用vuex
- wepy使用redux(wepy-redux)

> 待完善...