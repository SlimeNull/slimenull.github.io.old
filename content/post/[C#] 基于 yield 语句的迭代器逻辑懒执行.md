---
title: '[C#] 基于 yield 语句的迭代器逻辑懒执行'
slug: '[CSharp]基于yield语句的迭代器逻辑懒执行'
date: 2023-12-08 19:22:09
tags:
  - csharp
  - dotnet
categories:
  - dotnet
  - note
description: '众所周知, C# 可以通过yield语句来快速向或者类型的方法返回值返回一个元素. 但它还有另外一个特性, 就是其内部逻辑的懒执行. 每两个yield语句之间的逻辑都是一个状态, 只有在调用迭代器的MoveNext方法后, 才会执行下一个状态的逻辑.'
---

众所周知, C# 可以通过 `yield` 语句来快速向 `IEnumerator` 或者 `IEnumerable` 类型的方法返回值返回一个元素. 但它还有另外一个特性, 就是其内部逻辑的懒执行. 每两个 `yield` 语句之间的逻辑都是一个状态, 只有在调用迭代器的 `MoveNext` 方法后, 才会执行下一个状态的逻辑.


> 在文章中, 编译后的代码已经经过简化和删减, 以便于理解


<br/>


## 迭代器方法的懒执行

举一个简单的例子:


```csharp
IEnumerator SomeLogic()
{
    Console.WriteLine("hello world");
    yield return null;
    Console.WriteLine("fuck you world");
}
```

在调用的时候, 逻辑并不会被立即执行, 只有对其返回的迭代器调用 `MoveNext` 的时候, 才会继续执行.


```csharp
var enumerator = SomeLogic();

enumerator.MoveNext();   // 打印 hello world
enumerator.MoveNext();   // 打印 fuck you world
```



<br/>


## 迭代器方法的编译


在 C# 中, 使用了 yield 语句的, 返回 IEnumerator 或 IEnumerable 的方法, 会由编译器生成一个迭代器或可迭代类型, 在类型的内部包含该方法的逻辑.


例如上面提到的代码, 它会被编译成大概这样:


```csharp
IEnumerator SomeLogic()
{
    return new SomeLogicEnumerator();
}


private sealed class SomeLogicEnumerator : IEnumerator, IDisposable
{
    private int state;
    private object current;

    object IEnumerator.Current
    {
        get
        {
            return current;
        }
    }

    public SomeLogicEnumerator(int state)
    {
        this.state = state;
    }

    void IDisposable.Dispose()
    {
    }

    private bool MoveNext()
    {
        int num = state;
        if (num != 0)
        {
            if (num != 1)
            {
                return false;
            }
            state = -1;
            Console.WriteLine("AWA");
            return false;
        }
        state = -1;
        Console.WriteLine("QWQ");
        current = null;
        state = 1;
        return true;
    }

    bool IEnumerator.MoveNext()
    {
        //ILSpy generated this explicit interface implementation from .override directive in MoveNext
        return this.MoveNext();
    }

    void IEnumerator.Reset()
    {
        throw new NotSupportedException();
    }
}
```


我们可以看到, 在这个迭代器类型中, 有一个 `state` 字段存储了当前的状态, 而在 `MoveNext` 被调用时, 会切换当前状态, 然后根据当前状态执行对应逻辑.


当然, 如果你的迭代器逻辑稍微长一些, 它也是可以处理的.


```csharp
IEnumerator SomeLogic()
{
	Console.WriteLine("QWQ");
	yield return 1;
	Console.WriteLine("AWA");
	yield return 2;
	Console.WriteLine("QJFD");
	yield return 3;
	Console.WriteLine("JWOEIJFOIWE");
}
```


它的 `MoveNext` 就变成了这样:


```csharp
private bool MoveNext()
{
    switch (state)
    {
        case 0:
            Console.WriteLine("QWQ");
            current = 1;
            state = 1;
            return true;
        case 1:
            Console.WriteLine("AWA");
            current = 2;
            state = 2;
            return true;
        case 2:
            Console.WriteLine("QJFD");
            current = 3;
            state = 3;
            return true;
        case 3:
            state = -1;
            Console.WriteLine("JWOEIJFOIWE");
            return false;
        default:
            return false;
    }
}
```


