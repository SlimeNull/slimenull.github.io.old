---
title: '[C#] 在控制台绘图, 如:放置图像, 绘制线条'
slug: '[CSharp]在控制台绘图,如 放置图像,绘制线条'
date: 2020-11-15 21:59:01
tags:
  - csharp
  - .net
categories:
  - .NET
  - 控制台
  - 灌水
description: '[C#] 在控制台绘图原理: 通过Graphics进行绘图获取控制台的窗口句柄[DllImport("kernel32.dll")]static extern IntPtr GetConsoleWindow();获取Graphics对象Graphics g = Graphics.FromHwnd(GetConsoleWindow());于是乎, 你就可以通过获取的Graphics对象随便进行绘图了!但是, 注意, 当控制条刷新的时候, 比如Console.Clear(), 或者控制'
---

# [C#] 在控制台绘图

#### 原理: 通过Graphics进行绘图

1. 获取控制台的窗口句柄
    ```csharp
    [DllImport("kernel32.dll")]
    static extern IntPtr GetConsoleWindow();
    ```
2. 获取Graphics对象
    ```csharp
    Graphics g = Graphics.FromHwnd(GetConsoleWindow());
    ```

于是乎, 你就可以通过获取的Graphics对象随便进行绘图了!

> 但是, 注意, 当控制条刷新的时候, 比如Console.Clear(), 或者控制台光标经过绘图区域, 绘制的内容就会失效, 这时你需要重新绘制. (如果有控制台刷新的事件就好了)
