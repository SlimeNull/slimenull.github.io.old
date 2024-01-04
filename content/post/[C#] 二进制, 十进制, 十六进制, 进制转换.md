---
title: '[C#] 二进制, 十进制, 十六进制, 进制转换'
slug: '[CSharp]二进制,十进制,十六进制,进制转换'
date: 2021-04-06T22:37:59+08:00
tags:
  - csharp
  - 字符串
  - 进制
categories:
  - dotnet
  - note
  - 成长记录
description: '在源码中:C# 中允许在代码中使用 0x 开头的十六进制数字, 以及 0b 开头的二进制数字来表示一个整数. 下面的语法是合理的.int a = 123;int b = 0xFF;int c = 0b10010;数字转换将一个数字转换为某进制的字符串, 有两种方式:// 第一种: 使用 Convert 类. 可转换为 二进制 八进制 十进制 十六进制Convert.ToString(10, 2);    // 二进制, 返回 "1010"Convert.ToString(10, 8); '
---



### 在源码中:

C# 中允许在代码中使用 0x 开头的十六进制数字, 以及 0b 开头的二进制数字来表示一个整数. 下面的语法是合理的.

```csharp
int a = 123;
int b = 0xFF;
int c = 0b10010;
```


### 从数字转换

将一个数字转换为某进制的字符串, 有两种方式:

```csharp
// 第一种: 使用 Convert 类. 可转换为 二进制 八进制 十进制 十六进制
Convert.ToString(10, 2);      // 二进制, 返回 "1010"
Convert.ToString(10, 8);      // 八进制, 返回 "12"
Convert.ToString(10, 10);     // 十进制, 返回 "10"
Convert.ToString(10, 16);     // 十六进制, 返回 "a"

// 第二种: 使用对象的 ToString() 方法. 只能转换为 十六进制
10.ToString("x");             // 返回 "a"
10.ToString("X");             // 返回 "A"
10.ToString("x2");            // 返回 "0a"
10.ToString("X2");            // 返回 "0A"

// 第三种: 使用 String 的静态方法 Format, 只能转换为 十六进制
string.Format("{0:x}", 10)    // 返回 "a"
string.Format("{0:X}", 10)    // 返回 "A"
string.Format("{0:x2}", 10)   // 返回 "0a", 即填充至宽2
string.Format("{0:X2}", 10)   // 返回 "0A", 同样填充
```


### 从字符串转换

将字符串转换为对应的数据, 有两种方式

```csharp
// 第一种: 使用 Convert类. 可转换 二进制 八进制 十进制 十六进制, 这里以 int 为例
Convert.ToInt32("1010", 2);   // 二进制, 返回 10
Convert.ToInt32("12", 8);     // 八进制, 返回 10
Convert.ToInt32("10", 10);    // 十进制, 返回 10
Convert.ToInt32("A", 16);     // 十六进制, 返回 10
```

```csharp
// 第二种: 使用对应数据类型的 Parse 方法, 只支持 十六进制, 这里以 int 为例
int.Parse("A", NumberStyles.HexNumber);  // 返回 10
```


### 从字符数组转换

最常用的就是将字符数组转换为十六进制字符串了, Linq 可以帮到我们许多

```csharp
byte[] array; // 假定这个数组有值

// 第一种: 对应上面的 Convert 类静态方法, 也是个人比较推荐的
string.Join(null, array.Select(v => Convert.ToString(v, 16).PadLeft(2, '0')));      // 返回小写的

// 第二种: 对应格式化方法
string.Join(null, array.Select(v => v.ToString("X2")));

// 第三种: 对应 String 静态 Format 方法
string.Join(null, array.Select(v => string.Format("{0:X2}", v)));
```
