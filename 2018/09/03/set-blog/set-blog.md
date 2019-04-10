---
title: 博客搭建
date: 2018-09-03 15:25:53
tags: blog
categories: blog
toc: true
description: 一直都想做一个自己的博客，一初是准备花一点段时间自己写一个博客站点的，仔细想想，自己完全从零开始是比较费时费力的。除了技术编码，还需要前期的UI设计和功能的考量，以及设计到一些后台数据的管理，都是比较繁琐的工作，另外考虑到自己写的很多地方也会考虑不全，写出的站点未必是稳定的，不够成熟。所以还是选择了使用现有的博客框架来做。(主要还是太懒)
---

一直都想做一个自己的博客，一初是准备花一点段时间自己写一个博客站点的，仔细想想，自己完全从零开始是比较费时费力的。除了技术编码，还需要前期的UI设计和功能的考量，以及设计到一些后台数据的管理，都是比较繁琐的工作，另外考虑到自己写的很多地方也会考虑不全，写出的站点未必是稳定的，不够成熟。所以还是选择了使用现有的博客框架来做。(主要还是太懒)
由于 博客一初就是准备搭建在github page上的，所以自然就想到了hexo框架，下面就介绍一下整个hexo建站的流程，也是作为小白的我的一些经验总结吧。

# 准备工作

- 首先是在github帐号建仓，![](http://wx2.sinaimg.cn/large/99acde33gy1fuwk3q7tjsj20pd0liwfw.jpg)

repository name填入gitName.github.io,例如*wekerSnail.github.io* *这边是为了待会访问能直接用这个地址访问，这是固定格式*

- 安装node  安装完成可以通过命令`npm -v //或者 node -v` 查看是否能输出版本号，输出则安装成功
- 安装git
- 通过npm 安装hexo cli ` npm install hexo -g //全局安装`



# 开始搭建

*前面已经把必要的工具基本准备完成，现在开始着手配置和搭建*

## 初始化hexo

- 首先在本地创建一个文件夹，例如`mkdir blog`

  通过hexo 初始化该文件夹`hexo init`

  此时基本的hexo博客已经完成，只是都是默认设置，博客中有一篇hello world的实例文章

  通过`hexo g`命令生产页面

  `hexo s` 打开本地的预览，命令运行完会默认提示localhost:4000地址打开查看

- 新建一片文章 `hexo n 'name' `  hexo会在source目录生成一个md文件，通过编辑该文件来写博客文章

## 配置hexo

- 打开_config.yml文件

  找到deploy：

  填上自己相关的配置

  ```js
  deploy: 
  type: git
  repo: 这里填入你之前在GitHub上创建仓库的完整路径，记得加上 .git
  branch: master //注意hexo的配置项值的前面都有一个小空格
  ```

  配置结束安装部署插件

  ```bash
  npm install hexo-deployer-git --save
  ```

  安装完成就可以开始部署了，输入以下三条命令

  ```bash
  hexo clean //清除缓存
  hexo g //生成页面
  hexo d //部署页面
  ```

  结束就已经完成部署，此时可以浏览器打开 gitName.github.io来查看线上页面





# 查看文章

- [hexo参考](https://hexo.io/zh-cn/) `hexo 的一些基本命令已经使用方式还是需要熟悉的，方便管理自己的文章`
- [markdown语法](https://www.jianshu.com/p/0130ad32a08d) `//hexo是通过markdown语法来书写博客的，属于基本的markdown语法需要熟悉`