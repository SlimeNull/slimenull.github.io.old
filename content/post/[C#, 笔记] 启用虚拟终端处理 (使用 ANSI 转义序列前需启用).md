---
title: '[C#, 笔记] 启用虚拟终端处理 (使用 ANSI 转义序列前需启用)'
date: 2022-11-07 06:00:11
tags:
  - c#
  - 开发语言
categories:
  - .NET
  - 笔记
  - 控制台
description: '无法使用 ANSI 转义序列, 无法通过 \e \1b 逃逸字符打印彩色或格式化内容, 通过调用 WinAPI 启用虚拟终端处理来解决问题'
---

不启用虚拟终端会导致:


> 无法使用 ANSI 转义序列, 无法通过 \e \1b 逃逸字符打印彩色或格式化内容


通过以下代码来在 Windows 上启用:


```csharp
internal class PlatformUtils
{
    private const int STD_OUTPUT_HANDLE = -11;
    private const uint ENABLE_VIRTUAL_TERMINAL_PROCESSING = 0x0004;
    private const uint DISABLE_NEWLINE_AUTO_RETURN = 0x0008;

    [DllImport("kernel32.dll")]
    private static extern bool GetConsoleMode(IntPtr hConsoleHandle, out uint lpMode);

    [DllImport("kernel32.dll")]
    private static extern bool SetConsoleMode(IntPtr hConsoleHandle, uint dwMode);

    [DllImport("kernel32.dll", SetLastError = true)]
    private static extern IntPtr GetStdHandle(int nStdHandle);

    [DllImport("kernel32.dll")]
    private static extern uint GetLastError();

    public static uint EnableVirtualTerminalProcessingOnWindows()
    {
        var iStdOut = GetStdHandle(STD_OUTPUT_HANDLE);
        if (!GetConsoleMode(iStdOut, out uint outConsoleMode))
        {
            return GetLastError();
        }

        outConsoleMode |= ENABLE_VIRTUAL_TERMINAL_PROCESSING | DISABLE_NEWLINE_AUTO_RETURN;
        if (!SetConsoleMode(iStdOut, outConsoleMode))
        {
            return GetLastError();
        }

        return 0;
    }
}
```


在运行前加上以下代码即可:

```csharp
if (RuntimeInformation.IsOSPlatform(OSPlatform.Windows))
    PlatformUtils.EnableVirtualTerminalProcessingOnWindows();
```
