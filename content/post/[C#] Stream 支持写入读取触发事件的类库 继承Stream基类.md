---
title: '[C#] Stream 支持写入读取触发事件的类库 继承Stream基类'
slug: '[CSharp]Stream支持写入读取触发事件的类库继承Stream基类'
date: 2021-02-03 03:02:37
tags:
  - csharp
categories:
  - dotnet
  - 类库
  - 笔记
description: '[C#] Stream 支持写入读取触发事件的类库实现了 :你可以将这个流类的实例提供给某些东西, 在它操作这个流时, 你可以通过事件来接收到消息, 并加以处理, 例如拒绝写入, 或在写入前判断写入的内容. 你可以稍微改动一下这个类以适应你的需求.应用场景: 例如你使用了 IronPython 库, 并使用它执行了一些操作, 你希望 IronPython 每次 print 时, 你都能获取到内容, 则, 你可以使用这个触发流(TriggerStream)类, 将 IronPython 引擎的标准输出'
---

## [C#] Stream 支持写入读取触发事件的类库

### 实现了 :

1. 你可以将这个流类的实例提供给某些东西, 在它操作这个流时, 你可以通过事件来接收到消息, 并加以处理, 例如拒绝写入, 或在写入前判断写入的内容. 你可以稍微改动一下这个类以适应你的需求.
2. 应用场景: 例如你使用了 IronPython 库, 并使用它执行了一些操作, 你希望 IronPython 每次 print 时, 你都能获取到内容, 则, 你可以使用这个触发流(TriggerStream)类, 将 IronPython 引擎的标准输出流设置为触发流的实例, 这样, 每当 IronPython 有内容输出时, 你都能获取到内容. 例如这个项目: [使用IronPython来制作一个支持py脚本操作内容的简易文本编辑器](https://github.com/SlimeNull/Null.TextEditor)
3. 应用场景: 例如, 你运行了一个 Process, 并且希望实时获取它的输出内容, 而不是运行完之后一次性获取所有内容, 同理, 你可以设置它的标准输出流为触发流实例, 然后每次它输出内容你都能接收到.

### 源代码 :

**1. 首先是仅仅包含触发器, 而不会有任何存储行为的类**

```csharp
using System;
using System.IO;

namespace Null.Library.TriggerStream
{
    class WriteStreamEventArgs : EventArgs
    {
        public byte[] Buffer;
        public int Offset, Count;

        public bool Denied = false;
    }
    class TriggerStream : Stream
    {
        public override bool CanRead => false;

        public override bool CanSeek => false;

        public override bool CanWrite => true;

        public override long Length => 0;

        public override long Position { get => 0; set {  } }

        public override void Flush() { }

        public override int Read(byte[] buffer, int offset, int count)
        {
            return 0;
        }

        public override long Seek(long offset, SeekOrigin origin)
        {
            return 0;
        }

        public override void SetLength(long value) { }

        public override void Write(byte[] buffer, int offset, int count)
        {
            if (PreviewWrite != null)
                PreviewWrite.Invoke(this, new WriteStreamEventArgs()
                {
                    Buffer = buffer,
                    Offset = offset,
                    Count = count,
                });
        }
        public event EventHandler<WriteStreamEventArgs> PreviewWrite;
    }
}

```

很明显能够看出, 上面的这个, 仅有事件触发, 而Seek, Read, Position等方法及属性, 都是直接使用的空或者返回合适的固定值.


**2. 然后是带有存储功能的(使用MemoryStream)类**

```csharp
using System;
using System.IO;

namespace Null.Library.EventedStream
{
    class ReadStreamEventArgs : EventArgs
    {
        public byte[] Buffer;
        public int Offset, Count;

        public bool Denied = false;
    }
    class WriteStreamEventArgs : EventArgs
    {
        public byte[] Buffer;
        public int Offset, Count;

        public bool Denied = false;
    }
    public class FlushStreamEventArgs : EventArgs
    {
        public bool Denied = false;
    }
    public class SetStreamLengthEventArgs : EventArgs
    {
        public long Value;

        public bool Denied = false;
    }
    public class SeekStreamEventArgs : EventArgs
    {
        public long Offset;
        public SeekOrigin SeekOrigin;

        public bool Denied = false;
    }
    class EventedStream : Stream, IDisposable
    {
        MemoryStream baseMemory = new MemoryStream();
        public override bool CanRead => baseMemory.CanRead;

        public override bool CanSeek => baseMemory.CanSeek;

        public override bool CanWrite => baseMemory.CanWrite;

        public override long Length => baseMemory.Length;

        public override long Position { get => baseMemory.Position; set => baseMemory.Position = value; }

        public override void Flush()
        {
            if (PreviewFlush != null)
            {
                FlushStreamEventArgs args = new FlushStreamEventArgs();
                PreviewFlush.Invoke(this, args);
                if (args.Denied)
                    return;
            }

            baseMemory.Flush();
        }

        public override int Read(byte[] buffer, int offset, int count)
        {
            if (PreviewRead != null)
            {
                ReadStreamEventArgs args = new ReadStreamEventArgs()
                {
                    Buffer = buffer,
                    Offset = offset,
                    Count = count
                };
                PreviewRead.Invoke(this, args);
                if (args.Denied)
                    return 0;
            }

            return baseMemory.Read(buffer, offset, count);
        }

        public override long Seek(long offset, SeekOrigin origin)
        {
            if (PreviewSeek != null)
            {
                SeekStreamEventArgs args = new SeekStreamEventArgs()
                {
                    Offset = offset,
                    SeekOrigin = origin
                };
                PreviewSeek.Invoke(this, args);
                if (args.Denied)
                    return Position;
            }

            return baseMemory.Seek(offset, origin);
        }

        public override void SetLength(long value)
        {
            if (PreviewSetLength != null)
            {
                SetStreamLengthEventArgs args = new SetStreamLengthEventArgs()
                {
                    Value = value
                };
                PreviewSetLength.Invoke(this, args);
                if (args.Denied)
                    return;
            }

            baseMemory.SetLength(value);
        }

        public override void Write(byte[] buffer, int offset, int count)
        {
            if (PreviewWrite != null)
            {
                WriteStreamEventArgs args = new WriteStreamEventArgs()
                {
                    Buffer = buffer,
                    Offset = offset,
                    Count = count
                };
                PreviewWrite.Invoke(this, args);
                if (args.Denied)
                    return;
            }

            baseMemory.Write(buffer, offset, count);
        }
        public new void Dispose()
        {
            baseMemory.Dispose();
        }


        public event EventHandler<FlushStreamEventArgs> PreviewFlush;
        public event EventHandler<SetStreamLengthEventArgs> PreviewSetLength;
        public event EventHandler<SeekStreamEventArgs> PreviewSeek;
        public event EventHandler<WriteStreamEventArgs> PreviewWrite;
        public event EventHandler<ReadStreamEventArgs> PreviewRead;
    }
}

```

### 原理 :

倒也简单, 直接继承基类, 然后实现方法即可.

### 提示 :

如果你要同时使用这两个,,, 别忘记稍微移动下事件参数使它们在同一个文件中, 并使两个事件流类using事件参数的命名空间, 否则, 在不完全指定命名空间的状况下, 会出现不明确引用的错误.
