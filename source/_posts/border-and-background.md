---
title: 一些CSS背景和边框的小技巧
date: 2019-08-11 18:26:27
tags: 
- border-clip
- border-images
categories: css
---

## CSS盒模型

在正篇开始之前，我们先来复习一下CSS的标准盒模型。

![css盒模型](http://pw2l12hye.bkt.clouddn.com/css-box.png)

## 一、半透明边框

现在我们拥有一个矩形，为了接下来体现透明边框的效果，我们加上一个图片作为背景。

![](http://pw2l12hye.bkt.clouddn.com/border-background-1.png)

要实现一个半透明的边框，我们第一反应是使用rgba作为边框的颜色，但是看上去半透明边框并没有生效，why?


``` css
div {
  background: white;
  border: 20px solid rgba(255, 255, 255, 0.5);
}
```

![](http://pw2l12hye.bkt.clouddn.com/border-background-2.png)

实际上，在CSS2.1中，默认情况下，元素的背景会延伸到边框所在的区域下层，即使使用了不透明的边框，这一点也不会改变。我们把border的样式从solid改为dotted即可验证这一点。

``` css
div {
  border: 20px dotted black;
}
```

![](http://pw2l12hye.bkt.clouddn.com/border-background-3.png)

那么我们就没有办法实现半透明的边框了吗？当然不是！我们可以使用CSS3中的background-clip来做到。

``` css
div {
  border: 20px solid rgba(255, 255, 255, 0.5);
  background-clip: padding-box;
}
```

![](http://pw2l12hye.bkt.clouddn.com/border-background-4.png)

background-clip的所有属性：

background-clip: border-box | padding-box | content-box | text;

![](http://pw2l12hye.bkt.clouddn.com/border-background-5.png)

## 二、渐变边框

在实现了透明边框之后，我们学会了background-clip的使用，那如果是实现一个渐变的边框呢？话不多说，直接上代码。

``` css
div {
  background-image: linear-gradient(#ffffff, #ffffff), linear-gradient(#4ca2f9, #025cff);
  background-clip: padding-box, border-box;
  background-origin: border-box;
  border-left: 10px solid transparent;
}
```

![](http://pw2l12hye.bkt.clouddn.com/border-background-6.png)

我们给元素添加了background-image属性，并配合background-clip和background-origin属性实现了左边框的渐变，这是我在实际项目中实现的一个效果。可以看到background-image拥有两个渐变，分别对应background-clip的两个值，background-origin设置了背景的起点。为了更清楚的了解各个属性的关系，请看下面的例子

``` css
div {
  background-image: linear-gradient(#ffd2d2, #ff7777), linear-gradient(#ffffff, #ffffff), linear-gradient(#4ca2f9, #025cff);
  background-clip: content-box, padding-box, border-box;
  background-origin: border-box;
  border: 10px solid transparent;
}
```

![](http://pw2l12hye.bkt.clouddn.com/border-background-7.png)

## 三、条纹背景

上一小节中，我们使用了linear-gradient来实现渐变效果，下面我们来实现条纹背景。

``` css
div {
  background: linear-gradient(#fb3, #58a);
}
```

![](http://pw2l12hye.bkt.clouddn.com/border-background-8.png)

添加两个色标，分别是20%、80%。

``` css
div {
  background: linear-gradient(#fb3 20%, #58a 80%);
}
```

![](http://pw2l12hye.bkt.clouddn.com/border-background-9.png)

当我们把两个色标都设置成50%时会发生什么？

``` css
div {
  background: linear-gradient(#fb3 50%, #58a 50%);
}
```

![](http://pw2l12hye.bkt.clouddn.com/border-background-10.png)

没错，渐变效果消失了，再改变一下背景的高度。(为什么背景高度设置成30却填满了页面呢？)

``` css
div {
  background: linear-gradient(#fb3 50%, #58a 50%);
  background-size: 100% 30px;
}
```

![](http://pw2l12hye.bkt.clouddn.com/border-background-11.png)

duang!!!一个水平条纹的背景就实现了，我们还可以创建不等宽的条纹和多色的条纹

``` css
.div1 {
  background: linear-gradient(#fb3 30%, #58a 30%);
  background-size: 100% 30px;
}
.div2 {
  background: linear-gradient(#fb3 33.3%, #58a 33.3%, #58a 66.6%, yellowgreen 66.6%);
  background-size: 100% 45px;
}
```

![](http://pw2l12hye.bkt.clouddn.com/border-background-12.png)

再对代码进行一下优化，为了避免每次改动条纹宽度是都要修改多个数字，我们可以改变色标的值为0。

如果某个色标的位置值比整个列表中在它之前的色标的位置值都要小，则该色标的位置值会被设置为它前面所有色标位置值的最大值。

``` css
div {
  background: linear-gradient(#fb3 50%, #58a 0);
  background-size: 100% 30px;
}
```

![](http://pw2l12hye.bkt.clouddn.com/border-background-13.png)

实现了水平条纹，垂直条纹就很容易写出来了。

``` css
div {
  background: linear-gradient(to right, #fb3 50%, #58a 0);
  background-size: 30px 100%;
}
```

![](http://pw2l12hye.bkt.clouddn.com/border-background-14.png)

那斜条纹呢？

``` css
div {
  background: linear-gradient(45deg, #fb3 50%, #58a 0);
  background-size: 30px 30px;
}
```

![](http://pw2l12hye.bkt.clouddn.com/border-background-15.png)

显然这并不是我们期望的效果，增加一些色标试试。可以看到背景是由一个个小方块无缝拼接成的，只有这样才能的形成一个完整的背景。但此条纹的宽度却并不是我们想要的15px了，想要改变成15px的话，需要利用勾股定理。

``` css
div {
  background: linear-gradient(45deg, #fb3 25%, #58a 0, #58a 50%, #fb3 0, #fb3 75%, #58a 0);
  background-size: 30px 30px;
}
```

![](http://pw2l12hye.bkt.clouddn.com/border-background-16.png)

45度的条纹背景可以这样实现，但其他倾斜角度的就没有办法无缝拼接了，此时到了repeating-linear-gradient大显神通的时候了~

使用repeating-linear-gradient可以简单地创造出重复线性渐变，而且我们可以直接在属性里指定色标的宽度(在这里也就是条纹的宽度)，不再需要手动计算出background-size。

``` css
div {
  background: repeating-linear-gradient(60deg, #fb3, #fb3 15px, #58a 0, #58a 30px);
}
```

![](http://pw2l12hye.bkt.clouddn.com/border-background-17.png)

## 四、蚂蚁行军

学习了这么多之后，来个实用的例子~

``` css
div {
  border: 1px solid transparent;
  background-image: linear-gradient(white, white),repeating-linear-gradient(-45deg, black 0, black 25%, white 0, white 50%);
  background-clip: padding-box, border-box;
  background-position: 20px 20px;
  background-size: 10px 10px;
  animation: ants 12s linear infinite;
}

@keyframes ants {
  to {
    background-position: 100%;
  }
}
```
![](http://pw2l12hye.bkt.clouddn.com/border-background/ant.gif)