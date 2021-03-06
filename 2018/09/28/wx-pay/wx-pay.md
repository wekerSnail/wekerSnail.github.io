---
title: 微信支付IOS的坑点
date: 2018-09-28 10:58:22
tags: wx
categories: wx
toc: true
random_pics: 44
description: 移动端项目的公众号内需要一个下单支付的功能，一初的开发结束是拿安卓做了一下测试，ok，没有问题，于是就提交给了测试。结果在测试那边使用到ios手机是，首先是报签名错误，然后点击微信支付报url未注册，也就是配置的支付目录不正确。
---


# 问题

移动端项目的公众号内需要一个下单支付的功能，一初的开发结束是拿安卓做了一下测试，ok，没有问题，于是就提交给了测试。

结果在测试那边使用到ios手机是，首先是报签名错误，然后点击微信支付报url未注册，也就是配置的支付目录不正确。

# 分析

遇到问题之后，首先是检查了一下代码，调试输出各个参数，发现没有参数错误，于是就有点懵了。

google了一下ios微信签名错误的问题，发现有不少类似的问题。查阅了几篇下来，差不多都有提到ios的一个特性，就是vue的history在IOS下，页面跳转，而地址栏是不刷新的。但是此时我们使用location.href取取值的话，取到的值是正确的值，也就是说此时的地址栏和location.href的值是不一致的，这就导致了微信签名是我们的取值和微信jssdk的取值其实是不一致的，最后导致了签名的失败

# 解决方法

既然是因为url的不一致导致的签名失败，那么我们只需要让url的值一致就可以了。

前面我们也知道了是由于ios的原因导致vue的跳转实际地址栏没有变化，那么我们签名的时候只需要使用我们一初进入时候的页面地址就可以了。所以我们可以在`app.vue的create或者mounted`中去获取一下当前的地址，并缓存起来，然后在需要签名的地方在取出来使用就ok了。

这里需要注意的时，安卓端并无此问题，安卓是会正常刷新地址栏的，所以这边的签名获取需要有所区别，`url: isAndroid() ? 当前地址 : 缓存地址`。

按照上面的方法修改之后，在测试，果然ios和安卓的签名都没有问题了。**不过签名的问题解决了，ios支付的问题仍然存在**，根据提示的警告信息，可以看出是地址没有配置，但是我们明明配置了地址啊。所以这边也不难想到，也是签名ios的特性导致的地址没刷新，所以到了支付的时候的地址其实还是一初进入的地址，而不是我们配置的地址，所以微信这边就识别出错了。

解决方法依然是一样的思路，更新地址就行，这边没法用缓存了，因为是微信自己去取的，我们只能让页面刷新一下地址才行，那么我们有两个选择

- 前一个页面使用location.href=url去跳转
- 当前支付页面location.reload()刷新一下

我们选择了后者，1.我们页面有一些参数等，使用location.href则要自己拼上，比较麻烦，也容易出错，2.不使用vue的router跳转会让我们在路由导航写的一些方法失效。

所以还是刷新页面比较适合我们，为了保证页面的渲染性能，我们在`created`的周期中就开始刷新页面，使用localstorage去记录是否是首次进入，只有首次进入才需要刷新，刷新完就不需要刷新了

```js
 created() {
	    if (!localStorage.reload) {
	      localStorage.reload = 1;
	      window.location.reload();
	      return;
	    } else {
	    }
	  },
	  mounted() {
	    this._title = this.title;
	    fOpenId();
	    fJsTicket();
	  },
	  destroyed() {
	    localStorage.removeItem('reload')
	  }
```



可见，ios和安卓的不同之处还是比较多的，这只是我们遇到的其中之一个坑，填坑路漫漫，好在前人栽树后人乘凉，没有别人的经验分享，这些问题可能就会困扰我们很久才能解决