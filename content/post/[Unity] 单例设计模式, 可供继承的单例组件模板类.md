---
title: '[Unity] 单例设计模式, 可供继承的单例组件模板类'
date: 2023-08-28 20:22:36
tags:
  - unity
  - 设计模式
categories:
  - Unity
  - .NET
  - 笔记
description: 'Unity 单例模板类'
---

一个可供继承的单例组件模板类:


```cs
public class SingletonComponent<TComponent> : MonoBehavior
    where TComponent : SingletonComponent<TComponent>
{
    static TComponent _instance;

    private static TComponent GetOrFindOrCreateComponent()
    {
       // 双检索
        if (_instance == null)
        {
            // 尝试在场景中查找已存在的组件
            _instance = FindObjectOfType<TComponent>();
            
            // 如果找不到, 则创建一个空对象, 并且挂载上组件
            if (_instance == null)
            {
                GameObject gameObject = new GameObject();
                _instance = gameObject.AddComponent<TComponent>();
            }
        }

        return _instance;
    }

    public static TComponent Instance => GetOrFindOrCreateComponent();
}
```


> 因为 Unity 是单线程的, 所以在这里没有必要使用双检索


<br/>


## 使用方式


例如你要创建一个全局的单例管理类, 可以这样使用:

```cs
public class GameManager : SingletonComponent<GameManager>
{
    // your code here
}
```


<br/>


## 注意事项


尽量避免让 `SingletonComponent` 帮你创建组件, 因为它只是单纯的将组件创建, 并挂载到空对象上, 而不会进行任何其他行为. 如果你的组件需要进行某些初始化, 那么它可能不会正常.
