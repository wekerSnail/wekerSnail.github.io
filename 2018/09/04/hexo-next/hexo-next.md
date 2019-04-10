---
title: hexo-next主题设置
date: 2018-09-04 09:05:24
tags: blog
categories: blog
description: hexo搭建博客的自由度还是比较高的，特别是博客的主题非常多，有各种第三方的主题。通过主题的更换，可以让博客有不同的风格，并且切换的过程相对简单，方便。
---
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=26508242&auto=1&height=66"></iframe>  

hexo搭建博客的自由度还是比较高的，特别是博客的主题非常多，有各种第三方的主题。通过主题的更换，可以让博客有不同的风格，并且切换的过程相对简单，方便。

下面就以next主题为例，大致的梳理一下更换主题的过程：

1. 再hexo文件夹中，打开命令行工具，输入`git clone https://github.com/iissnan/hexo-theme-next themes/next`
   ,该命令会再`themes`文件夹下克隆`next`主题的仓库。
2. 修改hexo的配置
   - 打开hexo的配置文件`_config.yml`,找到`theme:`配置项，更改为`next`
   - 找到`site`项填上自己的博客名
   - 找到`language`项，填上`en//英文或者zh-Hans//中文`
3. 设置完成就可以再次部署
   命令行输入
```js
       hexo g
       hexo d
```
4. 部署成功之后，就可以再次回到自己的博客地址查看效果了，也可以`hexo s`先再本地预览一下效果

参考：

- next主题使用
- next官方文档
- maupassant主题参考
