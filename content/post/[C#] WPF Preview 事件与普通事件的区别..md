---
title: '[C#] WPF Preview 事件与普通事件的区别.'
slug: '[CSharp]WPFPreview事件与普通事件的区别.'
date: 2021-03-19T19:30:01+08:00
description: '很多文章都提到了冒泡事件和隧穿事件, 我是没有去测试过这两个的, 但是有一个非常非常重要的点很多人都忽略了.已预处理事件的控件在 WPF 中, 部分控件已经对某些事件进行了处理, 例如一个 Button, 它提供了 Click 事件, 而 Click 的本质是 MouseDown 和 MouseUp, 因而, Button 的 MouseDown 和 MouseUp 事件是没办法正常使用的…如果需要使用它们, 你得使用 PreviewMouseDown 和 PreviewMouseUp.同样, 有很'
---

很多文章都提到了冒泡事件和隧穿事件, 我是没有去测试过这两个的, 但是有一个非常非常重要的点很多人都忽略了.


### 已预处理事件的控件

在 WPF 中, 部分控件已经对某些事件进行了处理, 例如一个 Button, 它提供了 Click 事件, 而 Click 的本质是 MouseDown 和 MouseUp, 因而, Button 的 MouseDown 和 MouseUp 事件是没办法正常使用的...


如果需要使用它们, 你得使用 PreviewMouseDown 和 PreviewMouseUp.


同样, 有很多控件都是这样的, 例如 TextBox, 一个支持输入文本的控件, 同样还支持拖拽事件, 你可以向其中拖拽文本, 但是你不可以向其中拖拽图片, 这也是因为 TextBox 对拖拽事件进行了处理. 


也正因为如此, 即便你 TextBox 的 DragEnter 事件中指定了允许拖拽, 你也是不可以进行拖拽的! 因为 WPF 已经对这个事件进行处理了, 我们的逻辑不会起作用.
