---
title: "[C#] 任意频率 PCM 正弦波生成与 WAV 格式文件保存"
slug: "202408191228"
date: 2024-08-19T12:27:26+08:00
description: 
image: 
math: 
license: 
hidden: false
comments: false
draft: false
categories:
  - dotnet
tags:
  - dotnet
  - audio
---
封装一个简单的 `PcmBuilder`, 以 `IEnumerable<float>` 的形式提供采样值:

```csharp
public class PcmBuilder
{
    private PcmBuilder() { }

    public float SampleRate { get; set; } = 44100;
    public float Frequency { get; set; } = 4000;
    public float Amplitude { get; set; } = 1;

    public PcmBuilder WithSampleRate(long sampleRate)
    {
        SampleRate = sampleRate;
        return this;
    }

    public PcmBuilder WithFrequency(float frequency)
    {
        Frequency = frequency;
        return this;
    }

    public PcmBuilder WithAmplitude(float amplitude)
    {
        Amplitude = amplitude;
        return this;
    }

    public IEnumerable<float> Build()
    {
        float current = 0;
        while (true)
        {
            yield return MathF.Sin(current / SampleRate * Frequency) * Amplitude;

            current++;
        }
    }

    public static PcmBuilder CreateNew() => new PcmBuilder();
}
```

使用它构建出 44100Hz 采样率, 8000Hz 的正弦波:

```csharp
var pcm = PcmBuilder.CreateNew()
    .WithSampleRate(44100)
    .WithFrequency(8000)
    .Build();
```

WAV 文件的头格式:

![](/images/the_canonical_wave_file_format.jpg)

WAV 文件的头结构体定义:

```csharp
public unsafe struct WaveHeader
{
    private fixed byte _chunkId[4];
    private uint _chunkSize;
    private fixed byte _format[4];

    private fixed byte _subChunk1Id[4];
    private uint _subChunk1Size;
    private WaveAudioFormat _audioFormat;
    private ushort _numChannels;
    private uint _sampleRate;
    private uint _byteRate;
    private ushort _blockAlign;
    private ushort _bitsPerSample;

    private fixed byte _subChunk2Id[4];
    private uint _subChunk2Size;

    public unsafe string ChunkId
    {
        get
        {
            fixed (byte* ptr = _chunkId)
            {
                return CreateString(ptr, 4);
            }
        }
        set
        {
            fixed (byte* ptr = _chunkId)
            {
                FillString(ptr, value, 4);
            }
        }
    }

    public uint ChunkSize
    {
        get => _chunkSize;
        set => _chunkSize = value;
    }

    public string Format
    {
        get
        {
            fixed (byte* ptr = _format)
            {
                return CreateString(ptr, 4);
            }
        }
        set
        {
            fixed (byte* ptr = _format)
            {
                FillString(ptr, value, 4);
            }
        }
    }

    public string SubChunk1Id
    {
        get
        {
            fixed (byte* ptr = _subChunk1Id)
            {
                return CreateString(ptr, 4);
            }
        }
        set
        {
            fixed (byte* ptr = _subChunk1Id)
            {
                FillString(ptr, value, 4);
            }
        }
    }

    public uint SubChunk1Size
    {
        get => _subChunk1Size;
        set => _subChunk1Size = value;
    }

    public WaveAudioFormat AudioFormat
    {
        get => _audioFormat;
        set => _audioFormat = value;
    }

    public ushort ChannelCount
    {
        get => _numChannels;
        set => _numChannels = value;
    }

    public uint SampleRate
    {
        get => _sampleRate;
        set => _sampleRate = value;
    }

    public uint ByteRate
    {
        get => _byteRate;
        set => _byteRate = value;
    }

    public ushort BlockAlign
    {
        get => _blockAlign;
        set => _blockAlign = value;
    }

    public ushort BitsPerSample
    {
        get => _bitsPerSample;
        set => _bitsPerSample = value;
    }

    public string SubChunk2Id
    {
        get
        {
            fixed (byte* ptr = _subChunk2Id)
            {
                return CreateString(ptr, 4);
            }
        }
        set
        {
            fixed (byte* ptr = _subChunk2Id)
            {
                FillString(ptr, value, 4);
            }
        }
    }

    public uint SubChunk2Size
    {
        get => _subChunk2Size;
        set => _subChunk2Size = value;
    }

    public unsafe static WaveHeader Create(WaveAudioFormat audioFormat, ushort channelCount, uint sampleRate, ushort bitsPerSample, uint pcmDataSize)
    {
        return new WaveHeader()
        {
            ChunkId = "RIFF",
            ChunkSize = (uint)(pcmDataSize + (sizeof(WaveHeader) - 8)),
            Format = "WAVE",

            SubChunk1Id = "fmt ",
            SubChunk1Size = 16,
            AudioFormat = audioFormat,
            ChannelCount = channelCount,
            SampleRate = sampleRate,
            ByteRate = sampleRate * channelCount * bitsPerSample / 8,
            BlockAlign = (ushort)(channelCount * bitsPerSample / 8),
            BitsPerSample = bitsPerSample,

            SubChunk2Id = "data",
            SubChunk2Size = pcmDataSize
        };
    }

    private static void FillString(byte* ptr, string value, int maxLength)
    {
        if (value.Length > maxLength)
        {
            throw new ArgumentException(nameof(value));
        }

        fixed (char* textPtr = value)
        {
            for (int i = 0; i < value.Length && i < maxLength; i++)
            {
                ptr[i] = (byte)textPtr[i];
            }
        }
    }

    private static string CreateString(byte* ptr, int maxLength)
    {
        StringBuilder sb = new StringBuilder(maxLength);
        for (int i = 0; i < maxLength; i++)
        {
            if (ptr[i] == 0)
            {
                break;
            }

            sb.Append((char)ptr[i]);
        }

        return sb.ToString();
    }
}

public enum WaveAudioFormat : ushort
{
    None = 0,
    PCM = 1,
}
```

使用它构建 PCM 格式, 单声道, 44100Hz 采样率, 32位深, 30 秒的音频 WAV 头:

```csharp
var header = WaveHeader.Create(WaveAudioFormat.PCM, 1, 44100, 32, 44100 * 4 * 30);
```

创建文件, 并写入 WAV 头和 PCM 内容:

```csharp
using var output = File.Create("output.wav");
using var outputWriter = new BinaryWriter(output);

var pcm = PcmBuilder.CreateNew()
    .WithSampleRate(44100)
    .WithFrequency(8000)
    .Build();

var header = WaveHeader.Create(WaveAudioFormat.PCM, 1, 44100, 32, 44100 * 4 * 30);

// 写入 Header
unsafe
{
    var headerByteSpan = new Span<byte>((byte*)&header, sizeof(WaveHeader));
    outputWriter.Write(headerByteSpan);
}

// 获取 PCM 数据迭代器
var pcmEnumerator = pcm.GetEnumerator();

// 向文件写入 PCM
for (int i = 0; i < 44100 * 30; i++)
{
    pcmEnumerator.MoveNext();

    outputWriter.Write(pcmEnumerator.Current);
}
```