---
title: 'C# 动态输入'
slug: '20200406034849'
date: 2020-04-06T03:48:49+08:00
tags:
  - csharp
categories:
  - 控制台
  - 成长记录
  - 类库
description: 'C# 动态输入,在输入时你也可以访问你写入的内容1.缘由1.缘由. 最开始是我在写一个网络聊天程序(其实简陋的要命),然后服务端懒得写界面,就直接用的控制台,然后又想实现一些小指令,比如禁言,踢人,禁IP什么的,但是服务端在接收消息后就会直接将消息信息打印在控制台上例如这样 (下划线是光标所在处)老王 : 哎,房租又涨了,又得吃土了老张 : 啧啧啧,又在炫富了,我连土都莫得吃_但...'
---

### C# 动态输入,在输入时你也可以访问你写入的内容)

> 注意，这里是在我刚学C#时写的，但我不想删除任何我的足迹. 
> ==这个类库不是完善的，如果需要完整功能(真的很好用)，请去我的新文章==: [[C#] 控制台动态输入 - 增强版ReadLine()](/p/20201230223307/)

## 代码:

主要就是能够实现在输入的同时,子线程可以通过该实例的Text属性来访问已经输入了的内容,别的倒也懒得实现了awa


刚学不久,不喜勿喷


```csharp
//其实代码不怎么好,做做参考就可以了,注意:没有普通Console.ReadLine()的上下键功能

    class DynamicInput
    {
        class CharInfo
        {
            public CharInfo(int position,char chr)
            {
                this.position = position;
                this.chr = chr;
            }
            int position;
            char chr;
            public int Position
            {
                get
                {
                    return position;
                }
            }
            public char Char
            {
                get
                {
                    return chr;
                }
            }
        }

        delegate void Add_historyEventHandler(object sender,EventArgs e);

        private int default_left;
        private string text = "";
        private List<string> input_history = new List<string>();
        private List<CharInfo> input_list = new List<CharInfo>();

        public string Text
        {
            get
            {
                return text;
            }
        }
        public string Start()
        {
            default_left = Console.CursorLeft;
            ConsoleKeyInfo key;
            while (true)
            {
                key = Console.ReadKey();
                if (key.Key.Equals(ConsoleKey.Enter))
                {
                    return text;
                }        //回车确认
                if (key.Key.Equals(ConsoleKey.Backspace))
                {
                    Console.Write(" ");
                    if (Console.CursorLeft > input_list.Count)
                    {
                        if (input_list.Count >1 )
                        {
                            for (int i = 0; i <= Console.CursorLeft - input_list[input_list.Count-2].Position; i ++)
                            {
                                Console.Write("\b \b");
                            }
                        }
                        else
                        {
                            for (int i = 0; i <= Console.CursorLeft - default_left; i++)
                            {
                                Console.Write("\b \b");
                            }
                        }
                    }
                    else
                    {
                        if (input_list.Count > 1)
                        {
                            for (int i = 0; i <= Console.CursorLeft + Console.WindowLeft - input_list[input_list.Count - 2].Position; i++)
                            {
                                Console.Write("\b \b");
                            }
                        }
                        else
                        {
                            for (int i = 0; i <= Console.CursorLeft + Console.WindowLeft - default_left; i++)
                            {
                                Console.Write("\b \b");
                            }
                        }
                    }
                    if(input_list.Count > 0)
                    {
                        text = text.Substring(0, text.Length - 1);
                        input_list.RemoveAt(input_list.Count - 1);
                    }
                    continue;
                }    //BackSpace退格
                if (!key.Key.Equals(ConsoleKey.Spacebar) & (key.KeyChar == ' '|key.KeyChar == '\0'))
                {
                    Console.Write("\b");
                    continue;
                }
                text += key.KeyChar;                            //更新Text
                input_list.Add(new CharInfo(Console.CursorLeft, key.KeyChar));   //记录字符与位置
            }
        }
    }
```

