---
title: '[C#] Image的Image.GetThumbnailImage(获取缩略图)方法实际是缩放与拉伸'
slug: '[CSharp]Image的Image.GetThumbnailImage(获取缩略图)方法实际是缩放与拉伸'
date: 2020-05-26 05:10:54
tags:
  - dotnet
  - csharp
categories:
  - 桌面程序
  - 图片处理
description: '经过测试,Image.GetThumbnailImage 方法并不只是获取缩略图,你甚至可以拿它来放大图片,以及更多骚操作稍微包装一下,就得到了下面的函数,这可真是令人愉悦呀/// <summary>/// 缩放图片/// </summary>/// <param name="source">处理源</param>/// <param name="output">输出</param>/// <param name="'
---

#### 经过测试,Image.GetThumbnailImage 方法并不只是获取缩略图,你甚至可以拿它来放大图片,以及更多骚操作

##### 稍微包装一下,就得到了下面的函数,这可真是令人愉悦呀


```csharp
/// <summary>
/// 缩放图片
/// </summary>
/// <param name="source">处理源</param>
/// <param name="output">输出</param>
/// <param name="times">倍数</param>
/// <returns>表示操作是否成功</returns>
private bool GetMicroImage(Image source, out Image output, int times)
{
    try
    {
        output = source.GetThumbnailImage(source.Width * times, source.Height * times , () => false, IntPtr.Zero);
        return true;
    }
    catch
    {
        output = source;
        return false;
    }
}

```

#### 需要注意的是(GetThumbnailImage方法):

##### 1.缩放时,如果指定的宽高比例与原图宽高比例不同,则输出的图片较原图还进行了拉伸

也就是说,GetThumbnailImage 方法对原图处理时,并不是等比例缩放
