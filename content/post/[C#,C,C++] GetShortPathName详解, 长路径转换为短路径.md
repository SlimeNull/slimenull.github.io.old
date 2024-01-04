---
title: '[C#/C/C++] GetShortPathName详解, 长路径转换为短路径'
slug: '[CSharp,C,CPP]GetShortPathName详解,长路径转换为短路径'
date: 2021-02-09T08:05:35+08:00
tags:
  - csharp
  - dotnet
  - c++
  - win32
  - winapi
categories:
  - C++
  - dotnet
  - note
description: '说点骚话:转换需要用到 Windows API (废话)官方文档: GetShortPathNameW function (fineapi.h) - Win32 apps | Microsoft docs  (纯英文, 没有中文版本.)引用命名空间:using System.Runtime.InteropServices;关键代码:C#[DllImport("kernel32.dll", EntryPoint = "GetShortPathNameW", CharSet = CharSe'
---

## 说点骚话:

转换需要用到 Windows API (废话)


官方文档: [GetShortPathNameW function (fineapi.h) - Win32 apps | Microsoft docs](https://docs.microsoft.com/en-us/windows/win32/api/fileapi/nf-fileapi-getshortpathnamew)  (纯英文, 没有中文版本.)

## 引用命名空间: 

```csharp
using System.Runtime.InteropServices;
```

## 关键代码:

- csharp

```csharp
[DllImport("kernel32.dll", EntryPoint = "GetShortPathNameW", CharSet = CharSet.Unicode)]
extern static short GetShortPath(string longPath, string buffer, int bufferSize);

// 获取转换所需要的缓冲区大小
bool TryGetConvertBufferSize(string longPath, out short size)
{
    size = GetShortPath(longPath, null, 0);
    return size != 0;
}

// 进行转换
bool TryGetShortPath(string longPath, out string shortPath, int bufferSize = 256)
{
    string resultBuffer = new string('\0', bufferSize);               // 256 大概合适, 根据需求调整吧
    short rstLen = GetShortPath(longPath, resultBuffer, bufferSize);
    if (rstLen != 0 && rstLen != bufferSize)
    {
        shortPath = resultBuffer.TrimEnd('\0');
        return true;
    }
    else
    {
        shortPath = null;
        return false;
    }
}
```

- C++

```c
    long     length = 0;
    TCHAR*   buffer = NULL;

// 初次通过传递NULL和0来获取所需空间

    length = GetShortPathName(lpszPath, NULL, 0);
    if (length == 0) ErrorExit(TEXT("GetShortPathName"));

// 动态分配准确的内存

    buffer = new TCHAR[length];

// 现在, 使用同一个长路径来简单调用函数吧

    length = GetShortPathName(lpszPath, buffer, length);
    if (length == 0) ErrorExit(TEXT("GetShortPathName"));

    _tprintf(TEXT("long name = %s shortname = %s"), lpszPath, buffer);
    
    delete [] buffer;
```

## 更多说明:

#### 内容 1:

如果设置了 buffer, 与 bufferSize, 函数会尝试转换长路径到短路径, 如果返回的数字与 bufferSize 一致, 则表示缓冲区(buffer)太小.


如果 buffer 被设为 null, bufferSize 被设为 0, 则返回值将返回所需要的缓冲区大小.


无论如何, 如果返回值是 0, 则表示函数由于某些原因, 未能成功执行.


获取更多的错误信息, 请调用 [CallLastError](https://docs.microsoft.com/en-us/windows/desktop/api/errhandlingapi/nf-errhandlingapi-getlasterror)


**原文:**

> If the function succeeds, the return value is the length, in TCHARs, of the string that is copied to lpszShortPath, not including the terminating null character.
> &nbsp;
>If the lpszShortPath buffer is too small to contain the path, the return value is the size of the buffer, in TCHARs, that is required to hold the path and the terminating null character.
>&nbsp;
>If the function fails for any other reason, the return value is zero. To get extended error information, call [GetLastError](https://docs.microsoft.com/en-us/windows/desktop/api/errhandlingapi/nf-errhandlingapi-getlasterror).


#### 内容 2:


在这个函数(GetShortPathName)的 ANSI 版本中, 路径长度被限制在 MAX_PATH(260) 个字符. 要拓展这个限制到 23767 宽字符(wide character), 请调用这个函数的 Unicode 版本, 并且在路径的首部加上 "\\\\?\\". 更多信息, 请查阅 [文件, 路径, 以及命名空间的命名](https://docs.microsoft.com/en-us/windows/desktop/FileIO/naming-a-file)
**原文:**

> In the ANSI version of this function, the name is limited to MAX_PATH characters. To extend this limit to 32,767 wide characters, call the Unicode version of the function and prepend "\\?\" to the path. For more information, see Naming Files, Paths, and Namespaces.


#### 内容 3:

关于 Windows API 函数的 Unicode 版本和 ANSI 版本的区别, 可参阅: [Windows API 函数后缀的作用](https://blog.csdn.net/m0_46555380/article/details/109834245)
