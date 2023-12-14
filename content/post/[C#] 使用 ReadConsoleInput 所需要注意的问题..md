---
title: '[C#] 使用 ReadConsoleInput 所需要注意的问题.'
slug: '[CSharp]使用ReadConsoleInput所需要注意的问题.'
date: 2023-06-27 20:52:43
tags:
  - csharp
categories:
  - dotnet
  - 控制台
  - 笔记
description: '使用 ReadConsoleInput, 需要手动启用鼠标输入, 关闭快速编辑模式, 并删去任何读取标准输入的逻辑, 只使用 ReadConsoleInput'
---

如果要使用鼠标输入, 那么需要:

1. 启用鼠标输入, 只有启用之后, 才能接收到鼠标事件
2. 关闭控制台的快速编辑模式, 很多人的控制台都启用了快速编辑模式, 它会导致鼠标输入无效


任何情况下, 都需要:

- 不使用除 ReadConsoleInput 之外的任何输入方法, 因为它们会和你争抢 ReadConsoleInput 的使用.


引用:

- [GetStdHandle 函数](https://learn.microsoft.com/zh-cn/windows/console/getstdhandle)
- [GetConsoleMode 函数](https://learn.microsoft.com/zh-cn/windows/console/getconsolemode)
- [SetConsoleMode 函数](https://learn.microsoft.com/zh-cn/windows/console/setconsolemode)
- [ReadConsoleInput 函数](https://learn.microsoft.com/zh-cn/windows/console/readconsoleinput)


参考代码:


```cs
internal class NativeApi
{
    public const int STD_OUTPUT_HANDLE = -11;
    public const uint ENABLE_VIRTUAL_TERMINAL_PROCESSING = 0x0004;
    public const uint ENABLE_MOUSE_INPUT = 0x0010;
    public const int STD_INPUT_HANDLE = -10;
    public const uint ENABLE_QUICK_EDIT_MODE = 0x0040;

    [DllImport("kernel32.dll", SetLastError = true)]
    public static extern IntPtr GetStdHandle(int nStdHandle);

    [DllImport("kernel32.dll")]
    public static extern bool GetConsoleMode(IntPtr hConsoleHandle, out uint lpMode);

    [DllImport("kernel32.dll")]
    public static extern bool SetConsoleMode(IntPtr hConsoleHandle, uint dwMode);
    public static void EnableANSI()
    {
        var handle = GetStdHandle(STD_OUTPUT_HANDLE);
        GetConsoleMode(handle, out var mode);
        mode |= ENABLE_VIRTUAL_TERMINAL_PROCESSING;
        SetConsoleMode(handle, mode);
    }
    public static void EnableMouseInput()
    {
        var handle = GetStdHandle(STD_INPUT_HANDLE);
        GetConsoleMode(handle, out var mode);
        mode |= ENABLE_MOUSE_INPUT;
        mode &= ~ENABLE_QUICK_EDIT_MODE; //关闭快速编辑
        SetConsoleMode(handle, mode);
    }
    [DllImport("kernel32.dll", CharSet = CharSet.Unicode, SetLastError = true, EntryPoint = "ReadConsoleInputW")]
    public static extern bool ReadConsoleInput(
    IntPtr hConsoleInput,
    [Out] INPUT_RECORD[] lpBuffer,
    uint nLength,
    out uint lpNumberOfEventsRead);
    public enum InputRecordEventType : uint
    {
        KEY_EVENT = 0x1,
        MOUSE_EVENT = 0x2,
        WINDOW_BUFFER_SIZE_EVENT = 0x4,
        MENU_EVENT = 0x8,
        FOCUS_EVENT = 0x10,
    }

    [StructLayout(LayoutKind.Explicit)]
    public struct INPUT_RECORD
    {
        [FieldOffset(0)]
        public InputRecordEventType EventType;
        [FieldOffset(4)]
        public KEY_EVENT_RECORD KeyEvent;
        [FieldOffset(4)]
        public MOUSE_EVENT_RECORD MouseEvent;
        [FieldOffset(4)]
        public WINDOW_BUFFER_SIZE_RECORD WindowBufferSizeEvent;
        [FieldOffset(4)]
        public MENU_EVENT_RECORD MenuEvent;
        [FieldOffset(4)]
        public FOCUS_EVENT_RECORD FocusEvent;
    };
    [StructLayout(LayoutKind.Explicit, CharSet = CharSet.Unicode)]
    public struct KEY_EVENT_RECORD
    {
        [FieldOffset(0), MarshalAs(UnmanagedType.Bool)]
        public bool bKeyDown;
        [FieldOffset(4), MarshalAs(UnmanagedType.U2)]
        public ushort wRepeatCount;
        // ConsoleKey
        [FieldOffset(6), MarshalAs(UnmanagedType.U2)]
        public ushort wVirtualKeyCode;
        [FieldOffset(8), MarshalAs(UnmanagedType.U2)]
        public ushort wVirtualScanCode;
        [FieldOffset(10)]
        public char UnicodeChar;
        [FieldOffset(12), MarshalAs(UnmanagedType.U4)]
        public ControlKeyState dwControlKeyState;
    }

    // dwControlKeyState bitmask
    [Flags]
    public enum ControlKeyState
    {
        RIGHT_ALT_PRESSED = 0x1,
        LEFT_ALT_PRESSED = 0x2,
        RIGHT_CTRL_PRESSED = 0x4,
        LEFT_CTRL_PRESSED = 0x8,
        SHIFT_PRESSED = 0x10,
        NUMLOCK_ON = 0x20,
        SCROLLLOCK_ON = 0x40,
        CAPSLOCK_ON = 0x80,
        ENHANCED_KEY = 0x100
    }
    [StructLayout(LayoutKind.Sequential)]
    public struct MOUSE_EVENT_RECORD
    {
        public COORD dwMousePosition;
        public MouseButtonState dwButtonState;
        public short MouseDelta;
        public ControlKeyState dwControlKeyState;
        public MouseEventFlags dwEventFlags;
    }

    [Flags]
    public enum MouseButtonState : short
    {
        FROM_LEFT_1ST_BUTTON_PRESSED = 0x1,
        RIGHTMOST_BUTTON_PRESSED = 0x2,
        FROM_LEFT_2ND_BUTTON_PRESSED = 0x4,
        FROM_LEFT_3RD_BUTTON_PRESSED = 0x8,
        FROM_LEFT_4TH_BUTTON_PRESSED = 0x10
    }
    [StructLayout(LayoutKind.Sequential)]
    public struct COORD
    {
        public short X;
        public short Y;

        public COORD(short X, short Y)
        {
            this.X = X;
            this.Y = Y;
        }
    };
    [Flags]
    public enum MouseEventFlags
    {
        MOUSE_MOVED = 0x1,
        DOUBLE_CLICK = 0x2,
        MOUSE_WHEELED = 0x4,
        MOUSE_HWHEELED = 0x8
    }
    public struct WINDOW_BUFFER_SIZE_RECORD
    {
        public COORD dwSize;

        public WINDOW_BUFFER_SIZE_RECORD(short x, short y)
        {
            dwSize = new COORD(x, y);
        }
    }
    [StructLayout(LayoutKind.Sequential)]
    public struct MENU_EVENT_RECORD
    {
        public uint dwCommandId;
    }
    [StructLayout(LayoutKind.Sequential)]
    public struct FOCUS_EVENT_RECORD
    {
        public uint bSetFocus;
    }
}
```
