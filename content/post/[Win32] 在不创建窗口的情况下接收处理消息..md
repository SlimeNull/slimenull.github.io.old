---
title: '[Win32] 在不创建窗口的情况下接收处理消息.'
slug: '20230328075624'
date: 2023-03-28T07:56:24+08:00
tags:
  - wpf
  - windows
  - microsoft
categories:
  - Windows
description: '不创建常规窗口, 而是创建 "仅消息" 窗口用来处理窗体消息'
---

如果你在使用一个控制台程序, 并且还希望处理一些全局快捷键, 那么这个方法是合适的.


> 这里说的不创建窗口指的是不创建可见的窗口


## 使用 MessageOnly 窗口

Windows 中存在一个特殊的窗体句柄 HWND_MESSAGE, 它的值是 -3, 当一个窗口的父窗口是它的时候, 这个窗口会成为 "仅消息窗口"


使用仅消息窗口可以发送和接收消息。 它不可见、没有 z 顺序、无法枚举且不接收广播消息。 该窗口只是调度消息。


若要创建仅消息窗口，请在 [CreateWindowEx](https://learn.microsoft.com/zh-cn/windows/win32/api/winuser/nf-winuser-createwindowexa) 函数的 hWndParent 参数中指定HWND_MESSAGE常量或现有仅消息窗口的句柄。 还可以通过在 SetParent 函数的 hWndNewParent 参数中指定HWND_MESSAGE，将现有窗口更改为仅消息窗口。


> 参考: [窗口功能:仅消息窗口 -Win32 apps | Microsoft Learn](https://learn.microsoft.com/zh-cn/windows/win32/winmsg/window-features#message-only-windows)


### WPF 的使用示例:

```cs
// 窗口创建的参数
HwndSourceParameters hwndSourceParameters =
    new HwndSourceParameters()
    {
        HwndSourceHook = Hook,
        ParentWindow = (IntPtr)(-3),    // a magic window handle
    };

// 创建窗口
hwndSource =
    new HwndSource(hwndSourceParameters);

// Hook 消息方法定义
IntPtr Hook(IntPtr hwnd, int msg, IntPtr wParam, IntPtr lParam, ref bool handled)
{
    // 这里处理消息
}
```
