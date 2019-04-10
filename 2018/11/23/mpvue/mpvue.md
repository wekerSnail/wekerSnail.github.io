---
title: mpvue
date: 2018-11-23 11:20:39
tags: 小程序
categories: 小程序
description: 使用mpvue开发小程序的一些总结
---

# mpvue-小程序开发

[有道笔记文档](http://note.youdao.com/noteshare?id=0a4e6668de883f2ec14c36bbc6a3d294)

## 主要特性

使用 `mpvue` 开发小程序，你将在小程序技术体系的基础上获取到这样一些能力：

- 彻底的组件化开发能力：提高代码复用性
- 完整的 `Vue.js` 开发体验
- 方便的 `Vuex` 数据管理方案：方便构建复杂应用
- 快捷的 `webpack` 构建机制：自定义构建策略、开发阶段 hotReload
- 支持使用 npm 外部依赖
- 使用 `Vue.js` 命令行工具 vue-cli 快速初始化项目
- H5 代码转换编译成小程序目标代码的能力

## 配套设施

`mpvue` 作为小程序版本的 `Vue.js`，在框架 SDK 之外，完整的技术体系还包括如下设施。

- [mpvue-loader](http://mpvue.com/build/mpvue-loader) 提供 webpack 版本的加载器
- [mpvue-webpack-target](http://mpvue.com/build/mpvue-webpack-target) webpack 构建目标
- [postcss-mpvue-wxss](http://mpvue.com/build/postcss-mpvue-wxss) 样式代码转换预处理工具
- [px2rpx-loader](http://mpvue.com/build/px2rpx-loader) 样式转化插件
- [mpvue-lint](http://mpvue.com/build/mpvue-lint) 开发辅助插件，包括语法检查，内存检查等功能
- [官方文档](http://mpvue.com/）

**mpvue-lint会对代码做格式化检查，对编码规范要求比较严格，如果不需要可以项目初始化时不安装，或者注释rules部分的配置**

```js
//build/webpack.base.config.js   
  module: {
    rules: [
      /*{
        test: /\.(js|vue)$/,
        loader: 'eslint-loader',
        enforce: 'pre',
        include: [resolve('src'), resolve('test')],
        options: {
          formatter: require('eslint-friendly-formatter')
        }*/
      },
      {
        test: /\.vue$/,
        loader: 'mpvue-loader',
        options: vueLoaderConfig
      },
```



##### mpvue使用vue式的路由跳转

- 插件支持`mpvue-router-patch`

- 使用

  ```
  // main.js
  import Vue from 'vue'
  import MpvueRouterPatch from 'mpvue-router-patch'
  
  Vue.use(MpvueRouterPatch)
  ```

- [文档](https://github.com/F-loat/mpvue-router-patch)

### 使用

```
# 3. 全局安装 vue-cli,安装vue时安装过就不需要在安装了
# 一般是要 sudo 权限的
$ npm install --global vue-cli@2.9

# 4. 创建一个基于 mpvue-quickstart 模板的新项目
# 新手一路回车选择默认就可以了
$ vue init mpvue/mpvue-quickstart my-project

# 安装完成npm run dev之后，会生成一个dist文件夹
# 使用微信开发者工具导入dist下的项目就可以运行小程序的代码了
```



#### 语法差异

1. 文件结构

   - 小程序文件由四个文件构成`json、wxml、wxss、js`

   - mpvue由两个文件构成三个文件构成`main.json、index.vue、main.js`

   - mpvue .vue文件结构

     ```js
     {}
     <template>
     //wxml
     </template>
     
     <style>
     //wxss
     </style>
     
     <script>
     export default {
         //js
     }
     </script>
     ```

   - main.js为mpvue入口文件

     ```js
     import Vue from 'vue' //引入vue
     import App from './index' //引入.vue组件
     
     const app = new Vue(App) //生产vue实例
     app.$mount() //挂载实例
     
     ```

2. 配置部分

   - 文件目录`src/app.json`
   - 配置参数和小程序官方一致[参考文档](https://developers.weixin.qq.com/miniprogram/dev/framework/config.html)

3. wxml

   - 小程序所用标签皆可直接在mpvue中直接使用
   - mpvue中直接使用div会编译成view标签
   - 小程序`wx:if wx:else`对应mpvue中`v-if v-else`
   - 小程序`wx:for` 对应mpvue中`v-for`
   - 小程序`<text>{{msg}}</text>`写法和mpvue一致
   - 小程序动态参数属写法`attr="{{attr}}"`对应mpvue`:attr="attr"`
   - 除此之外vue原生支持的动态绑定class mpvue也支持 `:class="class"`，但此处不支持函数式的class返回值

4. wxss

   - mpvue支持npm安装css预处理器`sass、less、stulys...`
   - mpvue会把style中的px转换成rpx，若不希望装换可写成大写的PX

5. js

   - mpvue的js写法基本保持了vue原生的写法，支持vue的所有生命周期，也可以使用小程序的生命周期
   - mpvue的数据变化支持双向绑定
   - mpvue可直接调用wx的api

6. vuex的使用

   - store.js文件 //使用基本和原生vuex一样

     ```js
     import Vue from 'vue' //引入vue
     import Vuex from 'vuex' //引入 vuex
     
     Vue.use(Vuex) //使用vuex
     
     const store = new Vuex.Store({
       state: {
         count: 0
       },
       mutations: {
         increment: (state) => {
           const obj = state
           obj.count += 1
         },
         decrement: (state) => {
           const obj = state
           obj.count -= 1
         }
       }
     })
     
     export default store
     
     ```


#### mpvue注意点

1. `mpvue-router-patch`的路由插件不是必须的，可以使用微信官方的路由api实现
2. mpvue新增页面之后，需要重启服务，重新编译新文件，否则新页面会无法加载
3. 小程序的事件 bind 需要替换为 @
4. mpvue不支持自定义指令
5. 小程序dom操作不友好，ref使用不好
6. 小程序html中引入数据不支持函数，类似class不支持函数（return value方式）