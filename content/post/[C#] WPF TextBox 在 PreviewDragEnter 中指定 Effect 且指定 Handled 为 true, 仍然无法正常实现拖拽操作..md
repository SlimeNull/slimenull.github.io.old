---
title: '[C#] WPF TextBox 在 PreviewDragEnter 中指定 Effect 且指定 Handled 为 true, 仍然无法正常实现拖拽操作.'
slug: '[CSharp]WPFTextBox在PreviewDragEnter中指定Effect且指定Handled为true,仍然无法正常实现拖拽操作.'
date: 2021-03-19 19:44:01
tags:
  - .net
  - c#
  - wpf
categories:
  - 灌水
  - 成长记录
  - 笔记
description: '在开始之前, 请先阅读这篇文章: [C#] WPF Preview 事件与普通事件的区别我们知道, 某些控件会对事件进行处理, 导致部分事件我们无法正常使用, 对于 TextBox, 显而易见的是关于拖拽的事件完全不能正常使用. 因而我们需要使用 Preview 事件.对于一套拖拽操作, 有以下过程:用户拖拽数据进入控件 (DragEnter)用户拖拽数据在控件上移动 (DragOver)用户拖拽数据在控件上松开鼠标 (Drop)对于一个控件, 必须指定这个控件的 AllowDrop 属性为'
---

在开始之前, 请先阅读这篇文章: [\[C#\] WPF Preview 事件与普通事件的区别](https://blog.csdn.net/m0_46555380/article/details/115014131)


我们知道, 某些控件会对事件进行处理, 导致部分事件我们无法正常使用, 对于 TextBox, 显而易见的是关于拖拽的事件完全不能正常使用. 因而我们需要使用 Preview 事件.


对于一套拖拽操作, 有以下过程:

1. 用户拖拽数据进入控件 (DragEnter)
2. 用户拖拽数据在控件上移动 (DragOver)
3. 用户拖拽数据在控件上松开鼠标 (Drop)


对于一个控件, 必须指定这个控件的 AllowDrop 属性为 true, 这个控件才可以接受拖拽操作


对于上述 3 个事件:

1. DragEnter 中, 我们需要写的是判断拖拽的数据的类型, 并设定拖拽效果(DragDropEffects), 指定是否已经处理当前事件(Handled)
2. DragOver 中, 与 DragEnter 中一致. 所以这两个事件, 可以通过同一个方法来进行订阅
3. Drop 中, 应该写对数据的处理, 例如将数据添加到界面中


注意! 在 DragEnter 和 DragOver 中, 必须指定 DragDropEffects 才表示允许拖拽. 如果不对 Effects 进行赋值, 那么 Drop 方法将不会被触发.


****

相信看完上面的内容, 你已经可以解决你的问题了, 如果无法解决问题, 请在下方评论, 我会继续完善这篇文章
