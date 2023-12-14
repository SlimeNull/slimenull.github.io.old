---
title: '[C#] 简易的聊天气泡(很简单的实现)'
date: 2020-04-22 08:10:50
tags:
  - .net
  - c#
  - windows
  - winform
categories:
  - 桌面程序
description: '效果图能满足我自己的需求了直接看代码吧真的很简单…这是一个类,直接复制粘贴过去就好,不需要什么引用class ChatBubble        {            public ChatBubble(Panel panel, Font font)            {                if (panel.Controls.Count != 0) thr...'
---

## 效果图

##### 能满足我自己的需求了

![效果预览](images/20200422080806892.png)

## 直接看代码吧

##### 真的很简单...

这是一个类,直接复制粘贴过去就好,不需要什么引用

```csharp
class ChatBubble
        {
            public ChatBubble(Panel panel, Font font)
            {
                if (panel.Controls.Count != 0) throw new Exception("指定Panel控件不为空!");
                ChatPlace = panel;
                BubbleFont = font;
                MsgMaxLength = panel.Width - (6 * 4 + 35 * 2);                       // 其中, 四个6为 图片与(容器以及消息文本框)的距离 ,两个35为两侧图片的大小.
            }

            readonly Panel ChatPlace;
            readonly Font BubbleFont;

            int NowY = 7;

            readonly int MsgMaxLength;

            public enum MsgPlace
            {
                Left,
                Right
            }              // 气泡创建的位置

            public void AddMsg(Image Photo, string Text, MsgPlace Place, string Name)
            {
                if (ChatPlace.Controls.Count != 0) NowY = ChatPlace.Controls[ChatPlace.Controls.Count - 2].Location.Y + ChatPlace.Controls[ChatPlace.Controls.Count - 1].Height + 7;

                PictureBox photo = new PictureBox();                // 头像
                Label nickname = new Label();                       // 昵称
                Label msg = new Label();                            // 消息内容

                photo.Size = new Size(35, 35);
                photo.SizeMode = PictureBoxSizeMode.StretchImage;
                photo.Image = Photo;

                nickname.AutoSize = true;
                nickname.MaximumSize = new Size(0, 0);
                nickname.Font = BubbleFont;
                nickname.Text = Name;

                msg.AutoSize = true;
                msg.Font = BubbleFont;
                msg.MaximumSize = new Size(MsgMaxLength, 0);
                msg.Text = Text;
                msg.BorderStyle = BorderStyle.FixedSingle;

                ChatPlace.Controls.Add(nickname);
                ChatPlace.Controls.Add(photo);
                ChatPlace.Controls.Add(msg);

                if (Place == MsgPlace.Left)
                {
                    photo.Location = new Point(7, NowY);
                    nickname.Location = new Point(photo.Location.X + photo.Width + 6, NowY);
                    msg.Location = new Point(nickname.Location.X, NowY + nickname.Size.Height);
                }
                else
                {
                    photo.Location = new Point(ChatPlace.Width - 7 - 35 - 17, NowY);
                    nickname.Location = new Point(photo.Location.X - 7 - nickname.Width - 17, NowY);
                    msg.Location = new Point(photo.Location.X - 7 - msg.Width - 17, NowY + nickname.Size.Height);
                    // 这里的减去17是除去滚动条的宽度
                }
            }
        }        // 简易聊天气泡
```


使用这个类时,是需要提供一个Panel控件对象作为气泡创建的位置.


可以添加两种消息,左侧或者右侧.


最重要的原理就是Label的AutoSize,通过这个,我们就可以省去很多麻烦,然后再计算一个气泡最大的宽度可以是多少,设置Label的最大宽度,于是又实现了自动换行.

##### 使用方式:

在自己的项目中添加这个类,之后实例化
实例化时需要提供一个panel控件实例来作为气泡创建的容器
之后要添加消息的时候直接使用实例的方法 AddMsg即可,参数中已经包含了用户头像(Photo),消息内容(Text),消息位置(Place),发消息的用户(Name).

## 你可能也需要这个

##### . 不一定与上面的代码有关,但与聊天气泡有关

```csharp
TestBox textBox1 = new TextBox();
int Lines = textBox1.GetLineFromCharIndex(textBox1.Text.Length) - 1;
//Lines即为textBox1中文本的行数(包括自动换行)
```

