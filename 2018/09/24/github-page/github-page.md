---
title: github-pages部署单页应用
date: 2018-09-24 15:25:53
tags: github
categories: github
toc: true
description: github提供了很好的gitpage用于部署页面，并且是免费的，如果只是一些静态的页面不需要跟本地数据库交互，就完全可以部署在gitpage上面。
---

![](http://wx2.sinaimg.cn/mw690/99acde33gy1fvlm8f96obj21fw0neto1.jpg)

github提供了很好的gitpage用于部署页面，并且是免费的，如果只是一些静态的页面不需要跟本地数据库交互，就完全可以部署在gitpage上面。

下面就通过一个简单的webpack打包的react页面的部署，来介绍一个如何部署静态页面到github。

- 首先来到github中该demo的源码仓库，通过新建分支，新建一个gh-pages的分支![新建分支](http://wx2.sinaimg.cn/mw690/99acde33gy1fvlm8xs6uij20k30a1aag.jpg)

- 新建完分支，来到页面上部的settings部分，点击进入![点击setting](http://wx3.sinaimg.cn/mw690/99acde33gy1fvlm90an95j20lb08waae.jpg)

- 在settings页面中，找到gitpage的地址，也就是该页面部署完后的访问地址![github page地址](http://wx3.sinaimg.cn/mw690/99acde33gy1fvlm92wysoj20ol0bmaas.jpg)

- 复制该地址，打开demo的webpack的配置文件package.json，添加homepage的配置![homepage设置](http://wx2.sinaimg.cn/mw690/99acde33gy1fvlm95ksnwj20gy0g2dh2.jpg)

- 修改完配置之后，运行npm run build打包demo代码，打包完成之后，打开gitdesktop，选择改github仓库中的gh-page分支![分支选择](http://wx4.sinaimg.cn/mw690/99acde33gy1fvlm98hgb7j20y10nrwfz.jpg)

- 将打包完的文件，也就是build文件夹下的文件同步进入该分支，同步完成就完成了整个demo的部署，直接访问之前赋值的地址就可以看到运行之后的页面了。

  ```
  打包的时候如果需要设置根目录的实话，需要把根目录设置成github上的仓库目录，不然有些静态资源会加载错误
  ```



  通过这样的方式，我们可以很方便的在github上分享自己的代码的同时，也可以直接让其他人查看到demo的最终效果，甚至可以通过该方式去部署一些自己的个人博客网站等等。