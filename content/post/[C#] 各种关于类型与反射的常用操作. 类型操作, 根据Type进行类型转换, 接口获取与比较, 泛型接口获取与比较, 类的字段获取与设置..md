---
title: '[C#] 各种关于类型与反射的常用操作. 类型操作, 根据Type进行类型转换, 接口获取与比较, 泛型接口获取与比较, 类的字段获取与设置.'
slug: '[CSharp]各种关于类型与反射的常用操作.类型操作,根据Type进行类型转换,接口获取与比较,泛型接口获取与比较,类的字段获取与设置.'
date: 2020-10-19T21:16:01+08:00
tags:
  - csharp
  - 反射
  - 接口
categories:
  - 成长记录
  - dotnet
description: '直切入正题本文章是面向初学者的一些资料注意: 存在即合理, 可能某些初学者认为这些东西并无必要, 但实际上它们有很大的用处获取类型(Type)对象object obj;Type objType = obj.GetType();判断类型是否可以转换这个方法同样可以判断B是否继承于A(可以是类和接口), 但是如果你要判断一个类是否继承了一个泛型接口, 不指定相同的泛型参数, 是无法判断成功的, 比如一个继承了Demo<string, string>接口的类在使用下面的方法来判断'
---

# 直切入正题

> 本文章是面向初学者的一些资料
> 注意: 存在即合理, 可能某些初学者认为这些东西并无必要, 但实际上它们有很大的用处

### 获取类型(Type)对象

```csharp
object obj;
Type objType = obj.GetType();
```

### 判断类型是否可以转换

> 这个方法同样可以判断B是否继承于A(可以是类和接口), 但是如果你要判断一个类是否继承了一个泛型接口, 不指定相同的泛型参数, 是无法判断成功的, 比如一个继承了Demo<string, string>接口的类在使用下面的方法来判断是否继承Demo<>接口时, 就无法获得正确的结果. 这种方法是严谨的.


例子表示了A是否派生B, 也就是B是否继承于A, 或者说B是否可以强制转换为A

```csharp
bool result = typeof(B).IsAssignableFrom(typeof(B));
```

### 获取类型继承的的接口 (包含泛型接口)

> - 获取的接口, 都是接口原型, 比如获取到的会是IDicrionary<,>, 而不是IDictionary<TKey, TValue>. 利用这个特性, 我们可以判断一个类型是否继承某个泛型接口, 而不需要指定详细的泛型参数.
> - 下面的原始接口指IDictionary<string,string>与IDictionary<,>之间的关系, IDictionary即原始接口 (这个概念并不是公认概念, 但是没有已经规定的概念来描述"原始接口", 所以在这里提出了这个概念)

```csharp
Type[] interfaces = typeof(A).GetInterfaces();
```

参考: 判断是否继承某个泛型接口并获取泛型参数:

```csharp
// 在这个方法中, 会比对targetType的原始接口是否与interfaceType一致
private static bool CheckInterface(Type targetType, Type interfaceType, out Type[] geneticTypes)
{
    foreach(Type i in targetType.GetInterfaces())
    {
        if (i.GetGenericTypeDefinition().Equals(interfaceType))  // GetGenericTypeDefinition() 即获取原始接口类型. 如果去除这个方法, 则是严谨的比较类型
        {
            geneticTypes = i.GetGenericArguments();
            return true;
        }
    }
    geneticTypes = null;
    return false;
}
```

### 类的字段获取与设置

以类型A为例:

```csharp
A obj = new A();
Type objType = typeof(A);
FieldInfo[] fieldInfos = objType.GetFields();     // 获取字段信息
object fieldValue = fieldInfos[0].GetValue(obj);  // 获取字段值
fieldInfos[0].SetValue(obj, "这是一个值");         // 设置字段的值
```

### 根据Type对象进行类型转换

例如, 将A转换为B

```csharp
A objA = new A();
object objB = Convert.ChangeType(objA, typeof(B));    // 返回了转换为B类型的对象的引用
```
