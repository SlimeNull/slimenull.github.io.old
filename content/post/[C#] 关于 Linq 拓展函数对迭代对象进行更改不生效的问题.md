---
title: '[C#] 关于 Linq 拓展函数对迭代对象进行更改不生效的问题'
slug: '[CSharp]关于Linq拓展函数对迭代对象进行更改不生效的问题'
date: 2021-04-09 10:09:35
tags:
  - .net
  - csharp
categories:
  - .NET
  - 笔记
  - 灌水
description: '偶然发现, 在使用 Linq 的 Select 方法时, 如果对被迭代对象进行更改, 那么这个更改是不会生效的'
---


> 偶然发现, 在使用 Linq 的 Select 方法时, 如果对被迭代对象进行更改, 那么这个更改是不会生效的

```csharp
// 定义我们自己要用的类型
class QWQ
{
    public int qwq;
    public string awa;
    public QWQ(){}
    public QWQ(int qwq){this.qwq=qwq;}
}
// 用来执行的方法
static void LinqExTest()
{
    List<QWQ> qwqs = Enumerable.Range(0, 10).Select(v => new QWQ(v)).ToList();
    // 在迭代的同时, 对原对象进行更改:
    qwqs.Select(v => v.awa = v.qwq.ToString());
    // 打印对象
    Console.WriteLine(string.Join("\n", qwqs.Select(v => $"qwq:{v.qwq}, awa:{v.awa}")));
}
```


最终执行效果是: 


```txt
qwq:0, awa:
qwq:1, awa:
qwq:2, awa:
qwq:3, awa:
qwq:4, awa:
qwq:5, awa:
qwq:6, awa:
qwq:7, awa:
qwq:8, awa:
qwq:9, awa:
```


显然, 对原对象的更改没有生效! 即便是改为通过索引来访问, 也是不彳亍:


```csharp
qwqs.Select((v, i) => qwqs[i].awa = v.qwq.ToString());
```


```txt
qwq:0, awa:
qwq:1, awa:
qwq:2, awa:
qwq:3, awa:
qwq:4, awa:
qwq:5, awa:
qwq:6, awa:
qwq:7, awa:
qwq:8, awa:
qwq:9, awa:
```




但是如果将 Select 语句改为 foreach 语句, 则会成功运行:


```csharp
foreach(var v in qwqs)
    v.awa = v.qwq.ToString();
```


```txt
qwq:0, awa:0
qwq:1, awa:1
qwq:2, awa:2
qwq:3, awa:3
qwq:4, awa:4
qwq:5, awa:5
qwq:6, awa:6
qwq:7, awa:7
qwq:8, awa:8
qwq:9, awa:9
```


