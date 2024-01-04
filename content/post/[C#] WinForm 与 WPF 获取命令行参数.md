---
title: '[C#] WinForm 与 WPF 获取命令行参数'
slug: '20210209030419'
date: 2021-02-09T03:04:19+08:00
tags:
  - csharp
  - winform
categories:
  - note
  - 成长记录
  - dotnet
description: '推荐方法:using System;Environment.GetCommandLineArgs();   // 返回 string[]注意, 与控制台程序入口处的string[] args相比较, 这个函数返回的结果是完整的命令行, 也就是包含程序自身路径. 例如我一个没有传递任何参数的程序:所以注意区分哦.其他:WinForm在 Program.cs 的 Main 入口参数处添加 string[] args, 然后你可以更改窗体的构造函数, 使其能够接收这个args.WPF暂时不'
---

### 推荐方法:

#### 1. Environment.GetCommandLineArgs();

```csharp
using System;
Environment.GetCommandLineArgs();   // 返回 string[]
```

注意, 与控制台程序入口处的string[] args相比较, 这个函数返回的结果是完整的命令行, 也就是包含程序自身路径. 例如我一个没有传递任何参数的程序:

![](images/20210209030243165.png)
所以注意区分哦.

#### 

### 其他:

- WinForm
    在 Program.cs 的 Main 入口参数处添加 string[] args, 然后你可以更改窗体的构造函数, 使其能够接收这个args.
- WPF
    暂时不知道.
