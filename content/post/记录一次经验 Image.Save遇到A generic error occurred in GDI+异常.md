---
title: '记录一次经验:Image.Save遇到A generic error occurred in GDI+异常'
slug: '20200826215919'
date: 2020-08-26T21:59:19+08:00
categories:
  - 成长记录
  - dotnet
description: '要点:先看自己路径是不是错了然后看自己的路径表达是不是不符合标准, 例如这样的"/ewq_00010.png", 它指向C:\根目录下的一个文件! 而.NET中一般不允许对那里进行写操作然后, 我就是在上述内容的情况上犯了错… “/ewq_00010.png"应该是”./ewq_00010.png", 使用这个点来表示, 它是一个相对路径我的解决过程Image 保存的Path是"/ewq_00010.png"在发现这个问题后, 我首先是检查了一下, 我写的路径是否是正确的, 比如, 目'
---

## 要点:

0. 先看自己路径是不是错了
1. 然后看自己的路径表达是不是不符合标准, 例如这样的"/ewq_00010.png", 它指向C:\根目录下的一个文件! 而.NET中一般不允许对那里进行写操作
2. 然后, 我就是在上述内容的情况上犯了错... "/ewq_00010.png"应该是"./ewq_00010.png", 使用这个点来表示, 它是一个相对路径

## 我的解决过程

- Image 保存的Path是"/ewq_00010.png"


1. 在发现这个问题后, 我首先是检查了一下, 我写的路径是否是正确的, 比如, 目录是否存在, 我使用VS的调试功能, 测试了一下, Directory.Exists(Path.GetDirectoryName("/ewq_00010.png")) 结果是true, 初步判断不是路径问题
2. 后来查资料, 发现可能是这个Image是FromFile生成的而报错, 但我的不是, 它完完全全是一个new出来的, 我又尝试性地指定ImageFormat.Png, 果然, 问题没有解决
3. 继续查资料, 发现有人使用FileStream解决了问题, 我也尝试, 然后失败了... 写入被拒绝, 查了查资料, 为什么FileStream写入被拒绝, 一堆乱七八糟的东西, 看都看不懂, 大概就是讲ASP.NET不能在C:\写入, 但是我又不是ASP.NET
4. 然后我尝试转个弯子, 用MemoryStream, 然后最后用File.WriteAllBytes写入试试, 最后, 我突然发现, 它提示我, 对C:/的写入被拒绝, 蛤??? 我的路径不是"/ewq_0010.png"吗??? 我脑子跟Linux联想了起来, 总不会... 单个斜杠指C:/吧?


> 这个输出路径, 其实是经过我的一个PathCombine函数拼合而成的路径, 它很简单, 但也因为它的判断很简单而嗝屁了


```csharp
public static string CombinePath(string path1, string path2)  // 简单粗暴路径拼合
{
    if (path2.Contains(':'))
    {
        throw new ArgumentException("第二个路径不可是绝对路径");
    }
    return path1.TrimEnd(new char[] { '/', '\\' }).Replace('\\', '/') + "/" + path2.TrimStart(new char[] { '/', '\\' }).Replace('\\', '/');
}
```

5. 然后我把这个函数改成了这样:


```csharp
public static string CombinePath(string path1, string path2)
{
    if (path2.Contains(':'))
    {
        throw new ArgumentException("第二个路径不可是绝对路径");
    }
    string result = path1.TrimEnd(new char[] { '/', '\\' }).Replace('\\', '/') + "/" + path2.TrimStart(new char[] { '/', '\\' }).Replace('\\', '/');
    if (result.Contains(':'))     // 简单粗暴判断你是否是绝对路径
    {
        return result;   // 如果是绝对路径就直接返回, 也不管他是否是对的
    }
    else
    {
        return "./" + result.Trim('/');   // 如果不是, 则加一个"./"来强调, '我这是相对路径'
    }
}
```

6. 然后... 程序成功跑起来了...


![在这里插入图片描述](images/2020082621482447.png)


> 最后, 这个程序是用于根据atlas图像索引表分解图像的, 如果你觉得有参考价值, 可以私信我
![在这里插入图片描述](images/20200826215228152.png)

