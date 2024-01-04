---
title: '[C#] 控制台动态输入 - 增强版Console.ReadLine(), 在ReadLine的过程中获取用户已经输入的内容或移动已经输入内容的位置'
slug: '20201230223307'
date: 2020-12-30T22:33:07+08:00
tags:
  - dotnet
  - csharp
categories:
  - dotnet
  - 类库
  - 控制台
description: '简介：这是一个类库，正如标题所说，它具有这两个最基本而又强大的功能，有时候，我们可能会需要在ReadLine的过程中就访问已经输入了的内容，但.NET又没有提供这样的功能。其实在之前已经写过一个文章，也是动态输入，但是太烂了地址：旧的动态输入功能：在ReadLine的时候就读取已经输入了的内容，提供了完整的封装移动已经输入了的内容，你可以在输入时就将输入内容移动到控制台的任意位置光标移动，插入和覆盖模式，HOME和END键的处理。字符输入事件，在用户按下后，会有两个事件触发，可以通过'
---

## 简介：

- 这是一个类库，正如标题所说，它具有这两个最基本而又强大的功能，有时候，我们可能会需要在ReadLine的过程中就访问已经输入了的内容，但.NET又没有提供这样的功能。

> 其实在之前已经写过一个文章，也是动态输入，但是太烂了地址：[旧的动态输入](/p/20200406034849/)

## 功能：

1. 在ReadLine的时候就读取已经输入了的内容，提供了完整的封装
2. 移动已经输入了的内容，你可以在输入时就将输入内容移动到控制台的任意位置
3. 光标移动，插入和覆盖模式，HOME和END键的处理。
4. 字符输入事件，在用户按下后，会有两个事件触发，可以通过这两个事件来过滤用户输入内容，例如，仅允许输入数字，只需要判断事件参数即可。 
5. 密码输入模式，与Linux的密码输入一致，不会显示任何内容，但功能按键以及输入事件仍然可用
6. 历史记录功能，它模拟的是Linux系统的命令行输入历史记录功能，比Windows的历史记录功能好那么一丢丢。
7. （后续会增加更多功能，如果我需要或者你需要的话。

## 原理：

- 其实就是对 Console.ReadKey() 的妙用

## 使用方式：

1. 实例化一个DynamicScanner对象。
2. 调用DynamicScanner的ReadLine()方法或者QuietReadLine()方法
3. 读取正在输入的内容，请使用类实例的InputtingString属性
4. 获取已输入内容的起始位置以及末端位置, 可使用StartLeft, StartTop, EndLeft, EndTop属性
5. CharInput事件是当字符输入并且已经录入后触发的，PreviewCharInput是字符已经输入，但是并未录入的事件，CharInput要求返回值指定是否停止输入，PreviewCharInput要求返回值指定是否取消录入


> 如果代码有部分可以优化的部分，或者你有好的点子，欢迎私信我哦~
> 2021 / 1 / 5: 更新了源代码, 优化了一个小细节: 当此次输入为空时, 不保存历史记录


## 源代码：

- 创建一个名为Null.DynamicScanner.cs的文件，粘贴以下内容，添加到你的项目中，即可使用

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace Null.Library
{
    public class DynamicScanner
    {
        int startTop, startLeft, endTop, endLeft, currentTop, currentLeft, tempEndTop, tempEndLeft;
        int inputIndex, historyIndex;
        bool insertMode = true, inputting = false, cursorVisible;
        private readonly object printting = false;                // 用于互斥锁
        ConsoleKeyInfo readedKey;
        private readonly List<List<char>> inputHistory;
        private List<char> inputtingChars;
        private string promptText = string.Empty;


        public delegate bool CharInputEventHandler(DynamicScanner sender, ConsoleKeyInfo c);
        public delegate bool PreviewCharInputEventHandler(DynamicScanner sender, ConsoleKeyInfo c);

        public event CharInputEventHandler CharInput;
        public event PreviewCharInputEventHandler PreviewCharInput;

        public DynamicScanner()
        {
            inputHistory = new List<List<char>>();
        }
        public string InputtingString
        {
            get
            {
                return inputtingChars == null ? string.Empty : new string(inputtingChars.ToArray());
            }
        }

        public int StartTop { get => startTop; }
        public int StartLeft { get => startLeft; }
        public int EndTop { get => endTop; }
        public int EndLeft { get => endLeft; }
        public int CurrentTop { get => currentTop; }
        public int CurrentLeft { get => currentLeft; }
        public bool IsInputting { get => inputting; }
        public string PromptText { get => promptText; set => promptText = value; }

        public static bool IsControlKey(ConsoleKey k)
        {
            return k == ConsoleKey.Enter ||
                k == ConsoleKey.UpArrow ||
                k == ConsoleKey.DownArrow ||
                k == ConsoleKey.LeftArrow ||
                k == ConsoleKey.RightArrow ||
                k == ConsoleKey.Insert ||
                k == ConsoleKey.Backspace ||
                k == ConsoleKey.Delete ||
                k == ConsoleKey.Home ||
                k == ConsoleKey.End;
        }

        private void InitReadLine()
        {
            startTop = Console.CursorTop;
            startLeft = Console.CursorLeft;
            inputIndex = 0;

            if (inputHistory.Count == 0 || inputHistory[inputHistory.Count - 1].Count != 0)
            {
                historyIndex = inputHistory.Count;
                inputHistory.Add(new List<char>());
            }
            else
            {
                historyIndex = inputHistory.Count - 1;
            }

            inputtingChars = inputHistory[historyIndex];

            inputting = true;
        }
        private bool DealInputChar()
        {
            switch (readedKey.Key)
            {
                case ConsoleKey.Enter:
                    Console.WriteLine();
                    return true;
                case ConsoleKey.UpArrow:
                    if (historyIndex > 0)
                    {
                        historyIndex--;
                        UpdateInputState();
                    }
                    break;
                case ConsoleKey.DownArrow:
                    if (historyIndex < inputHistory.Count - 1)
                    {
                        historyIndex++;
                        UpdateInputState();
                    }
                    break;
                case ConsoleKey.LeftArrow:
                    if (inputIndex > 0)
                    {
                        inputIndex--;
                    }
                    break;
                case ConsoleKey.RightArrow:
                    if (inputIndex < inputtingChars.Count)
                    {
                        inputIndex++;
                    }
                    break;
                case ConsoleKey.Insert:
                    insertMode = !insertMode;
                    break;
                case ConsoleKey.Backspace:
                    if (inputIndex > 0)
                    {
                        inputtingChars.RemoveAt(inputIndex - 1);
                        inputIndex--;
                    }
                    break;
                case ConsoleKey.Delete:
                    if (inputIndex < inputtingChars.Count)
                    {
                        inputtingChars.RemoveAt(inputIndex);
                    }
                    break;
                case ConsoleKey.Home:
                    inputIndex = 0;
                    break;
                case ConsoleKey.End:
                    inputIndex = inputtingChars.Count;
                    break;
                default:
                    if (inputIndex == inputtingChars.Count)
                    {
                        inputtingChars.Add(readedKey.KeyChar);
                    }
                    else
                    {
                        if (insertMode)
                        {
                            inputtingChars.Insert(inputIndex, readedKey.KeyChar);
                        }
                        else
                        {
                            inputtingChars[inputIndex] = readedKey.KeyChar;
                        }
                    }
                    inputIndex++;
                    break;
            }
            return false;
        }
        private void PrintInputString()
        {
            lock(printting)
            {
                cursorVisible = Console.CursorVisible;
                Console.CursorVisible = false;
                Console.SetCursorPosition(startLeft, startTop);

                Console.Write(promptText);

                if (inputIndex == inputtingChars.Count)
                {
                    for (int i = 0; i < inputtingChars.Count; i++)
                    {
                        Console.Write(inputtingChars[i]);
                    }
                    currentLeft = Console.CursorLeft;
                    currentTop = Console.CursorTop;
                }
                else
                {
                    for (int i = 0; i < inputtingChars.Count; i++)
                    {
                        if (inputIndex == i)
                        {
                            currentLeft = Console.CursorLeft;
                            currentTop = Console.CursorTop;
                        }
                        Console.Write(inputtingChars[i]);
                    }
                }

                tempEndLeft = Console.CursorLeft;
                tempEndTop = Console.CursorTop;

                while (Console.CursorTop < endTop || Console.CursorLeft < endLeft)
                {
                    Console.Write(' ');
                }

                endLeft = tempEndLeft;
                endTop = tempEndTop;

                Console.SetCursorPosition(currentLeft, currentTop);
                Console.CursorVisible = cursorVisible;
            }
        }
        private void UpdateInputState()
        {
            inputtingChars = inputHistory[historyIndex];
            inputIndex = inputtingChars.Count;
        }
        public void ClearDisplayBuffer()
        {
            lock(printting)
            {
                cursorVisible = Console.CursorVisible;
                Console.CursorVisible = false;
                Console.SetCursorPosition(startLeft, startTop);
                while (Console.CursorTop < endTop || Console.CursorLeft < endLeft)
                {
                    Console.Write(' ');
                }
                Console.SetCursorPosition(startLeft, startTop);
                Console.CursorVisible = cursorVisible;
            }
        }
        public void DisplayTo(int cursorLeft, int cursorTop)
        {
            lock(printting)
            {
                cursorVisible = Console.CursorVisible;
                Console.CursorVisible = false;
                Console.SetCursorPosition(cursorLeft, cursorTop);
                startLeft = cursorLeft; startTop = cursorTop;

                Console.Write(promptText);

                if (inputIndex == inputtingChars.Count)
                {
                    for (int i = 0; i < inputtingChars.Count; i++)
                    {
                        Console.Write(inputtingChars[i]);
                    }
                    currentLeft = Console.CursorLeft;
                    currentTop = Console.CursorTop;
                }
                else
                {
                    for (int i = 0; i < inputtingChars.Count; i++)
                    {
                        if (inputIndex == i)
                        {
                            currentLeft = Console.CursorLeft;
                            currentTop = Console.CursorTop;
                        }
                        Console.Write(inputtingChars[i]);
                    }
                }

                endLeft = Console.CursorLeft;
                endTop = Console.CursorTop;
                Console.CursorVisible = cursorVisible;
            }
        }
        public void SetInputStart(int cursorLeft, int cursorTop)
        {
            lock(printting)
            {
                cursorVisible = Console.CursorVisible;
                Console.CursorVisible = false;
                Console.SetCursorPosition(startLeft, startTop);
                while (Console.CursorTop < endTop || Console.CursorLeft < endLeft)
                {
                    Console.Write(' ');
                }
                Console.SetCursorPosition(cursorLeft, cursorTop);
                startLeft = cursorLeft; startTop = cursorTop;

                Console.Write(promptText);

                if (inputIndex == inputtingChars.Count)
                {
                    for (int i = 0; i < inputtingChars.Count; i++)
                    {
                        Console.Write(inputtingChars[i]);
                    }
                    currentLeft = Console.CursorLeft;
                    currentTop = Console.CursorTop;
                }
                else
                {
                    for (int i = 0; i < inputtingChars.Count; i++)
                    {
                        if (inputIndex == i)
                        {
                            currentLeft = Console.CursorLeft;
                            currentTop = Console.CursorTop;
                        }
                        Console.Write(inputtingChars[i]);
                    }
                }

                endLeft = Console.CursorLeft;
                endTop = Console.CursorTop;
                Console.CursorVisible = cursorVisible;
            }
        }
        public string ReadLine()
        {
            InitReadLine();
            PrintInputString();

            while (true)
            {
                readedKey = Console.ReadKey(true);
                if (PreviewCharInput != null && PreviewCharInput.Invoke(this, readedKey))
                {
                    continue;
                }

                if (DealInputChar())
                {
                    inputting = false;
                    return InputtingString;
                }

                PrintInputString();

                if (CharInput != null && CharInput.Invoke(this, readedKey))
                {
                    inputting = false;
                    return InputtingString;
                }
            }
        }
        public string QuietReadLine()
        {
            InitReadLine();
            PrintInputString();

            while (true)
            {
                readedKey = Console.ReadKey(true);
                if (PreviewCharInput != null && PreviewCharInput.Invoke(this, readedKey))
                {
                    continue;
                }

                if (DealInputChar())
                {
                    inputting = false;
                    return InputtingString;
                }

                if (CharInput != null && CharInput.Invoke(this, readedKey))
                {
                    inputting = false;
                    return InputtingString;
                }
            }
        }
    }
}

```

## 使用实例：

- 这是一个可以过滤输入的使用实例, 它仅允许用户输入数字, 并且支持使用WSAD按键控制已输入内容在控制台中的移动

```csharp
using System;
using Null.Library;

namespace LibraryTest
{
    class Program
    {
        static void Main(string[] args)
        {
            DynamicScanner scanner = new DynamicScanner();
            scanner.PreviewCharInput += Scanner_PreviewCharInput;
            scanner.CharInput += Scanner_CharInput;
            while(true)
            {
                string temp = scanner.ReadLine();
            }
        }

        private static bool Scanner_CharInput(DynamicScanner sender, ConsoleKeyInfo c)
        {
            Console.Title = $"Length: {sender.InputtingString.Length}, Inputed: {sender.InputtingString}";
            return false;
        }

        private static bool Scanner_PreviewCharInput(DynamicScanner sender, ConsoleKeyInfo c)
        {
            if (c.KeyChar >= '0' && c.KeyChar <= '9' || DynamicScanner.IsControlKey(c.Key))
            {
                return false;       // 表示不取消, 即:录入这个字符
            }
            else
            {
                switch (c.Key)       // 通过WSAD按键可以控制输入内容移动
                {
                    case ConsoleKey.W:
                        sender.SetInputStart(sender.StartLeft, sender.StartTop - 1);
                        break;
                    case ConsoleKey.S:
                        sender.SetInputStart(sender.StartLeft, sender.StartTop + 1);
                        break;
                    case ConsoleKey.A:
                        sender.SetInputStart(sender.StartLeft - 1, sender.StartTop);
                        break;
                    case ConsoleKey.D:
                        sender.SetInputStart(sender.StartLeft + 1, sender.StartTop);
                        break;
                }
                return true;
            }
        }
    }
}

```

<br/><br/><br/><br/><br/>

> 如果你觉得这些内容对你有帮助， 请一定要点个赞，或者收藏它，这是对我莫大的鼓励
> 也欢迎私信我，交个朋友也是非常棒的~
