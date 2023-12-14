---
title: '[Unity] Awake 与 Start 的使用, 为什么将 GetComponent 逻辑放到 Awake 中'
slug: '[Unity]Awake与Start的使用,为什么将GetComponent逻辑放到Awake中'
date: 2022-09-27 14:16:18
tags:
  - unity
  - csharp
  - 游戏引擎
categories:
  - dotnet
  - Unity
  - 笔记
description: '震惊, 在 Start 中初始化变量竟然会引发如此严重的问题! 性能下降, 逻辑异常, 到底是人性的扭曲还是道德的沦丧?'
---

如果你的 Unity 脚本是这么写的, 那么你需要注意了

```csharp
using UnityEngine;

public class MyScript : MonoBehaviour
{
    SpriteRenderer spriteRenderer;
    void Start()
    {
        spriteRenderer = GetComponent<SpriteRenderer>();
    }
}
```


### Unity 管线

Unity 不是完全渲染完 A 然后再去渲染 B 的, 而是先渲染 A 的一部分, 然后渲染 B 的一部分, 再渲染其他的一部分.


然后再次渲染 A 的一部分, 渲染 B 的一部分, 渲染其他的一部分


换做脚本, 假设有 ABC 三个对象, 执行的顺序应该是.


```
A.Method1
B.Method1
C.Method1

A.Method2
B.Method2
C.Method2

A.Method3
B.Method3
C.Method3
```

### Awake 和 Start

Awake  和 Start 就相当于上面的 Method1, Method2, 所有对象的 Awake 都被调用一遍后, 然后再调用所有对象的 Start


### 不同对象顺序

Unity 是单线程, 所以 ABC 的 Method1 这种, 也是依次调用的, 也就是说, A 在调用 Method1 的时候, B 的 Method1 还没有被调用, 只能 A 的执行完之后轮到 B, 然后轮到 C


### 未初始化的变量

如果你把一些变量的初始化放在 Start 中, 那么在调用这个对象的 Start 前, 它的这些变量, 是完全没有初始化的, 为 null


### 引用未初始化的变量

如果你在某个对象没有调用 Start 前, 也就是它的变量未初始化时, 就调用这个对象的脚本暴露出来的一些公开方法, 例如依赖于 spriteRenderer  的 "角色姿态变换", 那么, 由于 spriteRenderer 没有初始化, 你的程序就引发 NullReferenceException 了



假设 A 的 Start 中需要调用 B 的 "MethodQwq", 在 B 的 MethodQwq 中, 用到了 B 类的字段 "spriteRenderer", 但是, spriteRenderer 是在 B 的 Start 方法中被赋值的


当 A Start 执行时, 此时 B 的 Start 还没执行, B 的 spriteRenderer 为 null


A 的 Start 调用 B 的 MethodQwq, 因为 spriteRenderer 为空, 所以引发了 NullReferenceException


## 解决方案

统一在 Awake 方法中做变量的初始化, 统一在 Start 中做逻辑的开始执行. 这样在 Start 调用时, 所有对象的 Awake 已经调用过, 变量也全部初始化, 所以不会引发上述问题
 
