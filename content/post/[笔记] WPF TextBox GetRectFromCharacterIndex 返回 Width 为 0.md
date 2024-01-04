---
title: '[笔记] WPF TextBox GetRectFromCharacterIndex 返回 Width 为 0'
slug: '[笔记]WPFTextBoxGetRectFromCharacterIndex返回Width为0'
date: 2022-10-10T10:22:17+08:00
tags:
  - dotnet
  - wpf
categories:
  - dotnet
  - note
description: 'WPF TextBox GetRectFromCharacterIndex 返回 Width 为 0, 获取指定字符的位置与宽高'
---

GetRectFromCharacterIndex(int index) 只返回字符左边缘的信息, 包含位置与高度


```csharp
//
// Summary:
//     返回指定索引处字符前边缘的矩形。
//
// Parameters:
//   charIndex:
//     要检索其矩形的字符的从零开始的字符索引。
//
// Returns:
//     字符前边缘处指定字符处的矩形 索引，或 System.Windows.Rect.Empty（如果无法确定边界矩形）
//     
//
public Rect GetRectFromCharacterIndex(int charIndex);
```


如果要获取某个 index 的字符的位置与大小, 可以通过 GetRectFromCharacterIndex 的重载获取字符右边缘的信息, 然后计算宽度


```csharp
//
// Summary:
//     返回字符前缘或后边缘的矩形 指定的索引。
//
// Parameters:
//   charIndex:
//     要检索其矩形的字符的从零开始的字符索引。
//
//   trailingEdge:
//     true 以获得字符的后边缘; false 以获得前边缘的角色。
//
// Returns:
//     指定字符索引处字符边边缘的矩形，或 System.Windows.Rect.Empty (如果无法确定边界矩形)
//
// Exceptions:
//   T:System.ArgumentOutOfRangeException:
//     字符索引为负数或大于内容的长度。
public Rect GetRectFromCharacterIndex(int charIndex, bool trailingEdge);
```
