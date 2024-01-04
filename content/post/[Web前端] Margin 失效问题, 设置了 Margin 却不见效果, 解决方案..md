---
title: '[Web前端] Margin 失效问题, 设置了 Margin 却不见效果, 解决方案.'
slug: '20210307092631'
date: 2021-03-07T09:26:31+08:00
tags:
  - html
  - css
  - css3
  - html5
categories:
  - note
  - 成长记录
description: '首先演示下:.box1{    width: 300px;    height: 300px;}.box2{    width: 200px;    height: 200px;}.box3{    width: 100px;    height: 100px;}.border-with{    border: solid 1px pink;}.padding-with{    padding: 1px;}.style-pink{    background-colo'
---

首先演示下:

```css
.box1{
    width: 300px;
    height: 300px;
}
.box2{
    width: 200px;
    height: 200px;
}
.border-with{
    border: solid 1px purple;
}
.padding-with{
    padding: 1px;
}
.style-pink{
    background-color: pink;
}
.style-green{
    background-color: green;
}
.margin-able{
    margin: 10px;
}
.margin-fix{
    overflow: hidden;
}
```

```html
<div class="box1 style-pink"></div>
<div class="box1 style-pink margin-able">
	<div class="box2 style-green margin-able"></div>
</div>
```

效果:


<div align="center"><img src="https://img-blog.csdnimg.cn/20210307090937101.png"/></div>


发现, 绿色盒子我们明明定义了margin, 而顶部的margin却没有起作用.


审查元素, 发现是这个情况:


<div align="center"><img src="https://img-blog.csdnimg.cn/20210307091331513.png"/></div>


并且发现, 如果指定padding或border, 那么它们就会变得正常.

<div align="center"><img src="https://img-blog.csdnimg.cn/20210307091619161.png"/></div> 
<div align="center"><img src="https://img-blog.csdnimg.cn/20210307091836101.png"/></div> 


最好的修复方式是这样, 为父元素指定overflow为hidden:


<div align="center"><img src="https://img-blog.csdnimg.cn/20210307092049552.png"/></div> 


可以给出一个我猜测的结论. margin是只是指与其他元素的间距, 即便它叫外边距. 因为父元素已经与上面的元素间距为5, 并且子元素没办法与别的元素产生边距, 所以就相对于最上方的元素计算边距, 即相对于父元素的间距是0. 而如果我们指定了border或padding, 他就会相对这多出的控件来运算边距, 即此时的margin相对于父元素.


另外overflow也是一个奇妙的样式, 它还可以用来修复子元素float引起的高度塌陷问题.

