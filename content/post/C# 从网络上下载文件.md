---
title: 'C# 从网络上下载文件'
slug: 'CSharp从网络上下载文件'
date: 2020-04-16T03:23:54+08:00
tags:
  - 网络
  - csharp
  - dotnet
categories:
  - 网络
description: '之前一直不理解如何是从网络上下载文件的…自己试了试懂了FileStream file = File.CreatWrite(filePath);                          // 创建文件WebRequest.Creat(url).GetResponse().GetResponseStream().CopyTo(file); // 创建WebRequest对象,获取响应,...'
---

之前一直不理解如何是从网络上下载文件的...自己试了试懂了


```csharp
FileStream file = File.CreatWrite(filePath);                          // 创建文件

WebRequest.Creat(url).GetResponse().GetResponseStream().CopyTo(file); // 创建WebRequest对象,获取响应,获取响应流,将响应流写入到文件流

file.Close();                                                         //关闭文件

```


嗯,这种简洁的代码最喜欢了 (虽然简洁...但是没人愿意看的hhh)


或者也可以写成函数


```csharp
bool DownloadFile(string url, string toDirectory, string fileName, int timeout = 2000)    //返回值是是否下载成功
{
    string PathCombine(string path1,string path2)    //合成路径
    {
        if (path1.EndsWith("\\"))
        {
            path1 = path1.Substring(0, path1.Length - 1);
        }
        if (path2.StartsWith("\\"))
        {
            path2 = path2.Substring(1);
        }
        return path1 + "\\" + path2;
    }
        
    if(!Directory.Exists(toDirectory))
    {
        Directory.CreatDirectory(toDirectory);
    }
    
    FileStream file = File.OpenWrite(fileName);
    WebRequest request = WebRequest.Creat(url);
    request.Timeout = timeout; //设置请求超时为函数参数
    
    try
    {
        request.GetResponse().GetResponseStream().CopyTo(file);
    }
    catch
    {
        return false;
    }
    
    file.Close();
    return true;
```

嗯,这样好了一些,因为设置了请求超时
