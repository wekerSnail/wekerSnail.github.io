---
title: canvas接红包游戏
date: 2018-12-15 14:55:22
tags: canvas
categories: canvas
description: 基于canvas的接红包游戏
---

由于工作业务基本用不到canvas，所以之前对canvas也没有做多少了解，仅是知道canvas是html5中用于绘制图形使用，通过js可以绘制出任意的图形，也可以通过连续的绘制，来实现动画的效果。性能上要优于直接操作dom实现的动画，当然也比flash的动画性能要好。但是对于实际的用法并没有太多了解

不过最近看canvas应用挺多，用来做一些简单的动画或者游戏还是比较实用的，于是就研究了一下，也写了一个简单的游戏来实践一下。也就是下面要讲到的接红包游戏

接红包要实现的效果比较简单，红包从屏幕上部往下落，玩家操纵底部的人来接住落下的红包，接住就加分，接住炸弹则扣分，模型如下图
![接红包草图](http://wx2.sinaimg.cn/mw690/99acde33gy1fy9k84kx5zj206b08qwea.jpg)

从图上可以看出，这个游戏主要需要处理的就两个对象，一个红包对象，一个就是下面操作的接红包的人，所以我们需要编写的两个模型就是红包模型和人模型

# drawImage()
说下面的具体实现之前,首先说一下主要用到的canvas api,这边的游戏图形基本用的是图片来显示的，所以就需要用到canvas中的绘制图片的api

drawImage主要用三种用法

```js
1. context.drawImage(img,x,y);//仅提供图片和绘制的起始位置，绘制出的图片大小由图片本身决定

2. context.drawImage(img,x,y,width,height);//提供图片，以及起始位置和显示的宽高

3. context.drawImage(img,sx,sy,swidth,sheight,x,y,width,height);//这个是切图的用法，提供图片以及剪切的起始位置和宽高，以及绘制的起始位置和宽高
```

该游戏用到的都是切好的图片，所以理所当然用的也就是第二种drawImage的方式

# 主体部分

这边使用一个Game类来对游戏做全局的管理

Game类中需要初始化一些基本的参数，比如画布的初始化，游戏基本参数的初始化
- 屏幕大小参数
- 游戏状态
- 红包的数据集合
- 操作状态
- 时间
- 得分
...

```js
this.isEnd = false;//游戏结束
this.touching = false;//是否触摸财神
this.moneyList = [];//掉落集合
this.score = 0;
this.time = 60;//游戏时间
...
```


# 红包部分

### 红包初始化

首先要考虑的是单个红包需要的属性：
 - 红包的x轴起始位置
 - 红包的y轴起始位置
 - 红包的宽度
 - 红包的高度
 - 红包的类型
 - 红包的分值
 - 红包的状态 //正在下落，已被接住，没有接住
 - 红包的速度 //不同的红包下落的速度不相同

*根据这些我们就可以写出我们的红包类:*

```js
var money = function (x, ob) {
        this.x = x;
        this.y = 0;
        this.type = ob.type;
        this.status = 0;//0正在掉落，1接住 ，2没接住
        this.widths = ob.widths;
        this.heights = ob.heights;
        this.value=ob.value;
        this.status=ob.status;
        this.speed=ob.speed;
    }
```



- 实际上红包就几种类型，所以我们不需要像上面那样每个参数都一一传入，我们可以编写一个红包的类型集合，然后传入一个`type`，来获取那些固定的初始参数，比如`宽度，高度，分值，速度，类型`

### 红包绘制

参数初始化完成之后，就需要绘制出这个红包:*我们绘制的时候其实只需要绘制出正在下落的红包，接住和没接住错过的，都是看不到的，所以不需要绘制，所以这边就需要用到红包的状态*

```js
money.prototype.draw = function () {
        if (this.status == 0) {//0代表正在下落的状态
            game.ctx.drawImage(moneyEnum[this.type].image, this.x, this.y, moneyEnum[this.type].widths, moneyEnum[this.type].heights);
        }
    }
```

### 红包的接住操作

红包接没接住，其实就是看这个红包有没有和下面移动的小人有没有接触过，图形上来看，就是有没有重叠过

```js
money.prototype.drop = function () {
        //速度叠加
        this.y += moneyEnum[this.type].speed;//每次下落的距离
        var top = this.y
        var bottom = this.y + this.heights
        var left = this.x
        var right = this.x + this.widths
        var caiY = game.phone.h - game.rect.h
        if (this.status == 0 && caiY <= bottom && ((game.rect.x <= left && left <= (+game.rect.x + game.rect.w)) || (game.rect.x <= right && (+game.rect.x + game.rect.w) >= right))) {//
            this.status = 1;
            game.score += moneyEnum[this.type].value;//记录总分数
        } else if (this.y >= game.phone.h) {
            this.status = 2;
        }
        this.draw();
    }
```

这边写的比较随意，大致的意思就是比较红包的底部是不是低于小人，并且左边在小人两边之间，或者右边在小人两边之间，满足这个条件也就是他们有接触

当然满足这些的同时，红包还得是还在屏幕内，出了屏幕外当然不行，所以下面的判断就是把出屏幕的设置为没接住

# 小人部分

和红包部分基本类似，首先就是声明基本的参数类型

小人主要需要的参数
- x轴起始位置
- y轴起始位置
- 小人宽度
- 小人高度

小人只有左右移动的功能，所以四个参数基本够用了

```js
var body=function(){
    this.w=xx;//宽度
    this.h=xx;//高度
    this.x=0;//如果要居中就是手机屏幕宽度一半减去小人宽度一半
    this.y=game.phone.h-this.h //居于底部，所以y轴位置固定
}
```

# 小人移动

小人移动需要修改的仅仅是小人的x轴参数

```js
   body.prototype.move = function () {
        game.ctx.drawImage(game.cai, game.rect.x || 0, (game.phone.h - game.rect.h), game.rect.w, game.rect.h);
    }
```

# 整合

现在基本的模块都有了，需要的就是把他们整合起来，形成游戏

首先就是让红包落下来

## 红包下落
随机生成红包对象，加入红包的集合中

```js
    Game.prototype.addMoney = function (x) {//掉钱
        var random = Math.floor(Math.random() * 4);
        if (random == 0 && game.five_num_left >= 0 && !game.isEnd) {
            this.moneyList.push(new money(x, "five"));
        } else if (random == 2 && game.pig_num_left >= 0 && !game.isEnd) {
            this.moneyList.push(new money(x, "pig"));
        } else if (random == 3 && game.boom_num_left >= 0 && !game.isEnd) {
            this.moneyList.push(new money(x, "boom"));
        } else if (random == 5 && game.cloud_num_left >= 0 && !game.isEnd) {
            this.moneyList.push(new money(x, "cloud"));
        }
    }
```

整体游戏的绘制
由game主体去控制所有部分的绘制

```js
    Game.prototype.draw = function () {
        this.clear()//首先清屏
        this.moneyList.forEach(function (item) {
            item.drop();//调用红包各自的下落绘制
        });
        this.move()//这边可以不用参考，因为编写的时候我并没有写小人类，而是直接写入了game类中，所以这边直接调用的小人的移动绘制
        $('.score').html("得分:" + game.score)//得分更新
    }
```

剩下就是移动的事件编写了，目前移动的方法都已经写好了，但是小人移动的事件还没有

```js
      document.addEventListener('touchstart', touch, {passive: false}); //操作事件总共三个事件，开始触摸，移动，结束触摸
        document.addEventListener('touchmove', touch, {passive: false});
        document.addEventListener('touchend', touch, {passive: false});
        var that = this

        function touch(event) {//点击
            var event = event || window.event;
            event.preventDefault();
            switch (event.type) {
                case "touchmove": //移动操作的前提就是正在操作状态，然后把移动的x值付给小人，为了让手指居中，我们可以减去小人宽度一半
                    if (that.touching) {
                        var x = 0
                        if (event.touches[0].clientX - that.rect.w / 2 <= 0) {
                            that.rect.x = 0
                        } else if (event.touches[0].clientX >=that.rect.w / 2  && event.touches[0].clientX <=that.phone.w-that.rect.w/2) {
                            that.rect.x = event.touches[0].clientX - that.rect.w / 2
                        } else {
                            that.rect.x = that.phone.w - that.rect.w
                        }
                    }
                    break;
                case "touchstart": //开始触摸时，判断是否触摸的是小人的部分，不是小人就不处理，是小人就设置为正在操作状态
                    if (event.touches[0].clientX > game.rect.x && event.touches[0].clientX < game.rect.x + game.rect.w && event.touches[0].clientY > game.rect.y) {
                        that.touching = true
                    }
                    break;
                case "touchend": //结束触摸状态
                    that.touching = false
            }
        }

```

现在就剩让游戏开始了

```js
    Game.prototype.start = function () {
        var that = this
        var addInterval = setInterval(function () {//首先开始落红包，游戏没结束就持续的下落
            if (!that.isEnd) {
                that.addMoney(Math.random() * (that.phone.w - 50)); //50是估计值，此处应取红包最大宽度，防止落出屏幕
            } else {
                clearInterval(addInterval);
            }
        }, 500);
        var animate = function (now) { //调用主体的绘制部分
            that.clear();
            that.draw();
            if (!that.isEnd) {
                requestNextAnimationFrame(animate);
            }
        }
        var timedCount = function () { //游戏倒计时以及状态更新
            $(".time_out").html("剩余时间:" + that.time);
            if (that.time <= 0) {
                clearTimeout(that.clock);
                that.isEnd = true;
                change(2)
            } else {
                that.time = that.time - 1;
                that.clock = setTimeout(timedCount, 1000);
            }
        }
        timedCount();
        window.requestNextAnimationFrame(animate);
    }
```

# 结束

到这边，整个游戏的编写就基本上结束了,接下来只需要在需要的页面实例化一下`Game`就可以了
最后的效果
![游戏效果界面](http://wx1.sinaimg.cn/small/99acde33gy1fy9o371nocj208l0f5mxy.jpg)