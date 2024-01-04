---
title: '[Unity] 基于迭代器的协程底层原理详解'
slug: '[Unity]基于迭代器的协程底层原理详解'
date: 2023-12-13T15:37:16+08:00
tags:
  - unity
  - 游戏引擎
categories:
  - dotnet
  - Unity
  - note
description: 'Unity 协程的本质无非就是在合适的实际执行迭代器的MoveNext方法. 对当前正在等待的对象进行条件判断, 如果满足条件, 则MoveNext, 否则就不执行.'
---

Unity 是单线程设计的游戏引擎, 所有对于 Unity 的调用都应该在主线程执行. 倘若我们要实现另外再执行一个任务, 该怎么做呢? 答案就是协程.


协程本质上是基于 C# yield 迭代器的, 使用 yield 语法生成的返回迭代器的方法, 其内部的逻辑执行, 是 "懒" 的, 只有在调用 `MoveNext` 的时候, 才会继续执行下一步逻辑.


<br/>


## Unity 生命周期


我们知道, Unity 在运行的时候, 本质上是有一个主循环, 不断的调用所有游戏对象的各个事件函数, 诸如 `Update`, `LateUpdate`, `FixedUpdate`, 以及在这个主循环中, 进行游戏主逻辑的更新. 其中协程的处理也是在这里完成的.


Unity 在每一个游戏对象中都维护一个协程的列表, 该对象启动一个协程的时候, 该协程的迭代器就会被放置到 "正在执行的协程" 列表中. Unity 每一帧都会对他们进行判断, 是否应该调用 `MoveNext` 方法.


又因为迭代器有 "懒执行" 的特性, 所以就能够实现, 等待某些操作结束, 然后执行下一段逻辑.


> 关于迭代器懒执行, 参考: [[C#] 基于 yield 语句的迭代器逻辑懒执行](https://blog.csdn.net/m0_46555380/article/details/134885593)


<br/>


## 仿写协程


光是口述, 肯定是无法讲明白协程原理的, 下面将使用代码简单实现一个协程.


我们游戏引擎将有以下文件:


- `GameEngine` : 游戏引擎, 存储所有的游戏对象
- `GameObject` : 表示一个游戏对象, 将会存储其正在运行的协程
- `GameObjectStates` : 表示一个游戏对象的状态, 例如它是否已经启动, 是否被销毁
- `Coroutine` : 表示一个正在运行的协程
- `WaitForSeconds` : 表示一个要等待的对象, 它将使协程暂停执行指定秒数
- `Program` : 游戏引擎的主循环逻辑


以及用户的逻辑:


- `MyGameObject` : 用户自定义的游戏对象


<br/>


首先创建一个 `GameEngine` 类, 它将容纳当前创建好的所有游戏对象.


```csharp
public class GameEngine
{
    // 私有构造函数, 使外部无法直接被调用
    private GameEngine()
    { }

    // 单例模式
    public static GameEngine Current { get; } = new();

    // 所有的游戏对象
    internal List<GameObject> _allGameObjects = new();

    // 通过 ReadOnlyList 向外暴露所有游戏对象
    public IReadOnlyList<GameObject> AllGameObjects => _allGameObjects;
    public int FrameNumber { get; internal set; }
}
```


创建一个 `WaitForSeconds` 类, 它和 Unity 中的 `WaitForSeconds` 类一样, 用于在写成中通过 yield 返回实现等待指定时间.


```csharp
public class WaitForSeconds
{
    public WaitForSeconds(float seconds)
    {
        Seconds = seconds;
    }

    public float Seconds { get; }
}
```


接下来, 创建一个 `Coroutine` 类, 它表示一个正在运行的协程, 构造时, 传入协程要执行的逻辑, 也就是一个 `IEnumerator`. 其中, 包含一个 "当前的等待对象" 以及 "当前等待对象相关联的某些参数数据". 它的 `Update` 方法会在游戏主循环中不断被调用.


```csharp
using System.Collections;

public class Coroutine
{
    public Coroutine(IEnumerator enumerator)
    {
        Enumerator = enumerator;
    }

    public IEnumerator Enumerator { get; }

    // 当前等待对象
    object? currentWaitable;

    // 与当前等待对象相关联的参数信息
    object? currentWaitableParameter;

    public bool IsCompleted { get; set; }

    internal void Update()
    {
        // 如果当前协程已经结束, 就不再进行任何操作
        if (IsCompleted)
            return;

        // 如果当前没有要等待的对象
        if (currentWaitable == null)
        {
            // 执行迭代器的 "MoveNext"
            if (!Enumerator.MoveNext())
            {
                // 如果迭代器返回了 false, 也就是迭代器没有下一个数据了
                // 则表示当前协程已经运行结束, 做上标记, 然后返回
                IsCompleted = true;
                return;
            }

            // 如果当前等待对象是 "等待指定秒"
            if (Enumerator.Current is WaitForSeconds waitForSeconds)
            {
                // 保存当前等待对象
                currentWaitable = waitForSeconds;

                // 将当前时间作为参数存起来
                currentWaitableParameter = DateTime.Now;
            }
            else if (Enumerator.Current is Coroutine coroutine)
            {
                // 如果当前等待对象是另一个协程
                // 保存当前等待对象
                currentWaitable = coroutine;
            }
        }
        else   // 否则, 也就是当当前等待对象不为空时
        {
            // 如果当前等待对象是 "等待指定秒"
            if (currentWaitable is WaitForSeconds waitForSeconds)
            {
                DateTime startTime = (DateTime)currentWaitableParameter!;
                
                // 判断是否等待结束
                if ((DateTime.Now - startTime).TotalSeconds >= waitForSeconds.Seconds)
                {
                    // 如果等待结束, 那么就将当前等待对象置空
                    // 这样下一次被调用 Update 时, 就会通过调用迭代器 MoveNext
                    // 执行协程的下一段逻辑, 并且获取下一个等待对象
                    currentWaitable = null;
                }
            }
            else if (currentWaitable is Coroutine coroutine)
            {
                // 如果等待对象是协程, 并且对应协程已经执行完毕
                if (coroutine.IsCompleted)
                {
                    // 将当前等待对象置空
                    currentWaitable = null;
                }
            }
        }
    }
}
```


编写一个 `GameObjectStates`  来表示一个游戏对象的状态, 例如是否启动了, 是否被销毁了什么的.


```csharp
internal class GameObjectStates
{
    // 对应游戏对象
    public GameObject Target { get; }

    // 是否已经启动
    public bool Started { get; set; }

    // 是否已经被销毁
    public bool Destroyed { get; set; }

    public GameObjectStates(GameObject target)
    {
        Target = target;
    }
}
```


下面, 编写一个 `GameObject`, 因为协程是运行在游戏对象中的, 所以游戏对象会有一个容器来承载当前游戏对象正在运行的协程. 当然, 它也有 `Start` 和 `Update` 两个虚方法, 会被游戏的主逻辑调用.


```csharp
using System.Collections;

public class GameObject
{
    // 当前游戏对象的状态
    internal GameObjectStates States { get; }

    // 所有正在运行的协程
    List<Coroutine> coroutines = new();

    // 即将开始运行的协程
    List<Coroutine> coroutinesToAdd = new();

    // 将要被删除的协程
    List<Coroutine> coroutinesToRemove = new();

    public GameObject()
    {
        // 初始化状态
        States = new(this);

        // 将当前游戏对象添加到游戏引擎
        GameEngine.Current._allGameObjects.Add(this);
    }

    // 由游戏引擎调用的 Start 和 Update
    public virtual void Start() { }
    public virtual void Update() { }

    // 由游戏引擎调用的, 更新所有协程的逻辑
    internal void UpdateCoroutines()
    {
        // 将需要添加的所有协程添加到当前正在运行的协程中
        foreach (var coroutine in coroutinesToAdd)
        {
            coroutines.Add(coroutine);
        }

        coroutinesToAdd.Clear();

        // 更新当前所有协程
        foreach (var coroutine in coroutines)
        {
            coroutine.Update();

            // 如果当前协程已经执行完毕, 则将其添加到 "删除列表" 中
            if (coroutine.IsCompleted)
            {
                coroutinesToRemove.Add(coroutine);
            }
        }

        // 将准备删除的所有协程从当前运行的协程列表中删除
        foreach (var coroutine in coroutinesToRemove)
        {
            coroutines.Remove(coroutine);
        }

        coroutinesToRemove.Clear();
    }

    // 开启一个协程
    public Coroutine StartCoroutine(IEnumerator enumerator)
    {
        Coroutine coroutine = new(enumerator);
        coroutinesToAdd.Add(coroutine);

        return coroutine;
    }

    // 停止一个协程
    public void StopCoroutine(Coroutine coroutine)
    {
        coroutinesToRemove.Add(coroutine);
    }

    // 停止一个协程
    public void StopCoroutine(IEnumerator enumerator)
    {
        int index = coroutines.FindIndex(c => c.Enumerator == enumerator);
        if (index != -1)
            coroutinesToRemove.Add(coroutines[index]);
    }

    // 销毁当前游戏对象
    public void DestroySelf()
    {
        States.Destroyed = true;
    }
}
```


自定义一个游戏对象 `MyGameObject`, 它在 `Start` 时启动一个协程.


```csharp
using System.Collections;

class MyGameObject : GameObject 
{
    public override void Start()
    {
        base.Start();
        StartCoroutine(MyCoroutineLogic());
    }


    IEnumerator MyCoroutineLogic()
    {
        System.Console.WriteLine("Logic out");
        yield return StartCoroutine(MyCoroutineLogicInner());
        yield return new WaitForSeconds(3);
        System.Console.WriteLine("Logic out end");
    }

    IEnumerator MyCoroutineLogicInner() 
    {
        for (int i = 0; i < 5; i++)
        {
            yield return new WaitForSeconds(1);
            Console.WriteLine($"Coroutine inner {i}");
        }
    }
}
```


程序主逻辑, 创建自定义的游戏对象, 并执行主循环:


```csharp
// 创建自定义的游戏对象
new MyGameObject();

// 要被销毁的游戏对象
List<GameObject> objectsToDestroy = new();

while (true)
{
    // 对所有游戏对象执行 Start
    foreach (var obj in GameEngine.Current.AllGameObjects)
    {
        if (!obj.States.Started)
        {
            obj.Start();
            obj.States.Started = true;
        }
    }

    // 调用所有游戏对象的 Update
    foreach (var obj in GameEngine.Current.AllGameObjects)
    {
        if (obj.States.Destroyed)
            continue;
            
        obj.Update();
    }

    // 更新所有游戏对象的协程
    foreach (var obj in GameEngine.Current.AllGameObjects)
    {
        if (obj.States.Destroyed)
            continue;

        obj.UpdateCoroutines();
    }

    // 将需要被销毁的游戏对象存起来
    objectsToDestroy.Clear();
    foreach (var obj in GameEngine.Current.AllGameObjects)
    {
        if (obj.States.Destroyed)
            objectsToDestroy.Add(obj);
    }

    // 从游戏引擎中移出游戏对象
    foreach (var obj in objectsToDestroy)
        GameEngine.Current._allGameObjects.Remove(obj);
}
```


执行结果:


```txt
Logic out
Coroutine inner 0
Coroutine inner 1
Coroutine inner 2
Coroutine inner 3
Coroutine inner 4
Logic out end
```


<br/>


## 总结


综上所述, 可以了解到, Unity 协程的本质无非就是在合适的实际执行迭代器的 `MoveNext` 方法. 对当前正在等待的对象进行条件判断, 如果满足条件, 则 `MoveNext`, 否则就不执行.
