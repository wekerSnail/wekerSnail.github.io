---
title: 前端实现图片上传的预览功能
date: 2018-08-08 15:20:03
tags: js
categories: js
description: 使用html5中的文件控件，多个图片的上传预览
---

*使用html5中的文件控件，多个图片的上传预览*

图片的上传使用的是和文件上传一样的文件控件
```html
<input type='file' />
```
但是直接使用，会发现只可以选择一张图片，是因为file控件默认只接受一个参数，想要选择多个文件，需要使用multiple属性
>multiple 属性规定输入字段可选择多个值。
如果使用该属性，则字段可接受多个值。
例如：
```html
<input type='file' multiple=''/>
```
接下来开始进行多个图片的上传预览处理，

首先使用的是file控件的files属性
```js
var files=document.querySelector('.file').files
```
==此处如果使用jq的语法获取dom会导致获取不到files(jq获取的是jq对象，原生获取的是DOM对象，两者有区别)==

获取到的files数据是一个数组，所以我们的需要预览图片的话，需要遍历数组来取值

```js
  for (let i = 0; i < files.length; i++) {
                let file=files[i]//获取当前的file值
                var reader = new FileReader();  
                var img = '<img/>'
                reader.onloadend = function (e) {//readAsDataURL触发的loadend事件
                    img = '<img class=imgs src=' + e.target.result + '>'
                    $(img).appendTo($('#img'))//新生成的图片添加到图片预览的dom中
                };
                if (file) {//判断是否还有值
                    reader.readAsDataURL(file);//读取file文件
                }
        ｝

```
**FileReader**
>FileReader 对象允许Web应用程序异步读取存储在用户计算机上的文件（或原始数据缓冲区）的内容，使用 File 或 Blob 对象指定要读取的文件或数据。

>其中File对象可以是来自用户在一个`<input>`元素上选择文件后返回的FileList对象,也可以来自拖放操作生成的 DataTransfer对象,还可以是来自在一个HTMLCanvasElement上执行mozGetAsFile()方法后返回结果。

**readAsDataURL**
>该方法会读取指定的 Blob 或 File 对象。读取操作完成的时候，readyState 会变成已完成（DONE），并触发 loadend 事件，同时 result 属性将包含一个data:URL格式的字符串（base64编码）以表示所读取文件的内容。

```js
语法
instanceOfFileReader.readAsDataURL(blob);
```