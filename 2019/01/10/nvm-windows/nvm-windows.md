---
title: nvm-windows:windows下的node版本管理
date: 2019-01-10 13:22:04
tags: node
description: nvm是mac/linux系统上比较好用的一款node版本管理工具，然后它并不支持windows系统，好在有人另外开发了支持windows的node的多版本管理工具,nvm-windows
---
nvm是mac/linux系统上比较好用的一款node版本管理工具，然后它并不支持windows系统，好在有人另外开发了支持windows的node的多版本管理工具,nvm-windows

# 安装
windows提供直接下载的安装包 [下载](https://github.com/coreybutler/nvm-windows/releases)

*该工具不能和现有的node共存，如要安装使用，需要先写在本机上的node*

# 使用
```bash
nvm --help //可以调出nvm的使用帮助

nvm list available //查看所有的node版本

nvm install version 安装对应版本的node

nvm use version //使用对应版本的node

nvm list  //查看本机安装的node版本

nvm uninstall version //删除对应的版本node
```

`nvm use version` 切换需要的node版本，就可以正常使用node环境了，安装cnpm 镜像源也是没有问题了
