---
title: '[笔记] 记录一次该死的 “玄学“ Bug, 赶紧看看避开这个坑!!! .NET Core, Delegate.BeginInvoke, PlatformNotSupportedException'
date: 2021-03-09 21:48:03
tags:
  - debug
  - bug
  - .net
  - 多线程
  - 异步
categories:
  - 笔记
  - 成长记录
  - .NET
description: '概述:没事闲着别总是玩异步, 否则可能就会像我这样出现线程问题这件事:首先是… 报了一堆 “平台不支持的” 错误.堆栈显示也看不出来是哪里的问题, 但我看到了 Threading 这玩意儿…:最后通过 “死亡断点” 发现是这里出的问题, 这是一个异步操作的回调函数.进一步调试, 发现是这里, 这里又会 Invoke 一个事件:好家伙, 又是一个事件… 然后我看了看订阅了这个事件的地方…乍一看, 没啥毛病! 但问题确实出在这里! (我实在是太菜了)最后… 我思'
---

## 概述:

.NET Core 不支持委托的 BeginInvoke 方法, 使用别的方法, 例如 Task 来替代它!


## 这件事:


首先是... 报了一堆 "平台不支持的" 错误.


<div align="center">
<img src="https://img-blog.csdnimg.cn/20210309213418546.png"/>
</div>


堆栈显示也看不出来是哪里的问题, 但我看到了 Threading 这玩意儿...:


<div align="center">
<img src="https://img-blog.csdnimg.cn/20210309213525619.png"/>
</div>


最后通过 "死亡断点" 发现是这里出的问题, 这是一个异步操作的回调函数.


<div align="center">
<img src="https://img-blog.csdnimg.cn/20210309213723524.png"/>
</div>


进一步调试, 发现是这里, 这里又会 Invoke 一个事件:


<div align="center">
<img src="https://img-blog.csdnimg.cn/20210309213924137.png"/>
<img src="https://img-blog.csdnimg.cn/20210309214018874.png"/>
</div>


好家伙, 又是一个事件... 然后我看了看订阅了这个事件的地方...


<div align="center">
<img src="https://img-blog.csdnimg.cn/20210309214243836.png"/>
</div>


乍一看,,,, 没啥毛病! 但问题确实出在这里! (我实在是太菜了)
最后... 我思考了下, 我在 Invoke 调用的委托里面, 整了异步的操作...... 于是我试着将 BeginInvoke 换成 Invoke, 然后.... 问题解决了!!!


后来又双叒叕遇到了这个问题,,, 然后仔细搜索了下资料. 懂了. 原来 .NET Core 不支持委托的 BeginInvoke 方法. 而事件的本质就是多播委托!
