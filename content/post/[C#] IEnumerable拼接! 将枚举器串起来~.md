---
title: '[C#] IEnumerable拼接! 将枚举器串起来~'
slug: '[CSharp]IEnumerable拼接,将枚举器串起来~'
date: 2021-02-19T15:28:33+08:00
tags:
  - csharp
  - csharp
  - dotnet
categories:
  - note
  - dotnet
  - 类库
description: '本来以为 IEnumerable 不能拼接, 就自己实现了一个, 结果发现 Linq 是提供了一个 Concat 函数的, 不过似乎是通过生成List的方式来实现? 反正我那个是异步的.用来做参考还是非常不辍滴, 速度的话, 4个10000拼接, 然后重复迭代10000次, 我写的是3590ms, Linq 的是2931ms, 不过两个其实都没有直接 ToList() 然后迭代要快, ToList(), 然后AddRange, 其实耗时只有九百多毫秒, 足足差了3倍左右.static void Coll'
---

本来以为 IEnumerable 不能拼接, 就自己实现了一个, 结果发现 Linq 是提供了一个 Concat 函数的, 不过似乎是通过生成List的方式来实现? 反正我那个是异步的.

```csharp
using System;
using System.Collections;
using System.Collections.Generic;

namespace NullLib.Iterator
{
    public static class NIterator
    {
        public static IEnumerable Concat(this IEnumerable iterator1, IEnumerable iterator2)
        {
            foreach (var i in iterator1)
                yield return i;
            foreach (var i in iterator2)
                yield return i;
        }
        public static IEnumerable<T> Concat<T>(this IEnumerable<T> iterator1, IEnumerable<T> iterator2)
        {
            foreach (var i in iterator1)
                yield return i;
            foreach (var i in iterator2)
                yield return i;
        }
        public static IEnumerable Concat(this IEnumerable iterator1, params IEnumerable[] iterator2)
        {
            foreach (var i in iterator1)
                yield return i;
            foreach (var i in iterator2)
                foreach (var j in i)
                    yield return j;
        }
        public static IEnumerable<T> Concat<T>(this IEnumerable<T> iterator1, params IEnumerable<T>[] iterator2)
        {
            foreach (var i in iterator1)
                yield return i;
            foreach (var i in iterator2)
                foreach (var j in i)
                    yield return j;
        }
        public static IEnumerable Concat(params IEnumerable[] iterators)
        {
            foreach (var i in iterators)
                foreach (var j in i)
                    yield return j;
        }
        public static IEnumerable<T> Concat<T>(params IEnumerable<T>[] iterators)
        {
            foreach (var i in iterators)
                foreach (var j in i)
                    yield return j;
        }
    }
}
```
