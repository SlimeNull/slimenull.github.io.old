---
title: 'C# 循环所有可能的字符'
slug: '20201026155944'
date: 2020-10-26T15:59:44+08:00
categories:
  - 灌水
  - dotnet
description: '通过 char.MaxValue 来作为循环结尾, 将int强制转换为char, 即可之前自己搜索这个内容, 发现国内没有, 所以写了这个文章供参考for (int i = 0; i <= char.MaxValue; i++){    // 此处放处理语句, (char)i 即为当前字符}...'
---

> 通过 char.MaxValue 来作为循环结尾, 将int强制转换为char, 即可
> ~~之前自己搜索这个内容, 发现国内没有, 所以写了这个文章供参考~~ 


```csharp
for (int i = 0; i <= char.MaxValue; i++)
{
    // 此处放处理语句, (char)i 即为当前字符
}
```
 
