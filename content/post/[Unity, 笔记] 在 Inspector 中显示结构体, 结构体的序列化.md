---
title: '[Unity, 笔记] 在 Inspector 中显示结构体, 结构体的序列化'
slug: '[Unity,笔记]在Inspector中显示结构体,结构体的序列化'
date: 2023-08-31 15:02:36
tags:
  - csharp
  - unity
categories:
  - note
  - Unity
description: '给结构体添加 System.Serializable 特性就可以让结构体显示在 Inspector 中了'
---

在 Unity 中的 Inspector, 基本数据类型, 数组, 都是可以直接显示出来的, 但创建出来的结构体不能直接显示出来.


为结构体添加一个 `System.Serializable` 特性即可使其能够显示在 Inspector 中.


```cs
[Serializable]
struct CustomData
{
    public int SomeInteger;
    public string SomeString;
}
```
