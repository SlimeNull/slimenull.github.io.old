---
title: '[C#] NAudio 各种常见使用方式 播放 录制 转码 音频可视化'
date: 2021-05-06 19:49:04
tags:
  - 可视化
  - 音频编码解码
  - c#
categories:
  - 笔记
description: '概述在 NAudio 中, 常用类型有 WaveIn, WaveOut, WaveStream, WaveFileWriter, WaveFileReader, AudioFileReader 以及接口: IWaveProvider, ISampleProvider, IWaveIn, IWavePlayerWaveIn 表示波形输入, 继承了 IWaveIn, 例如麦克风输入, 或者计算机正在播放的音频流.WaveOut 表示波形输出, 继承了 IWavePlayer, 用来播放波形音乐, 以 I'
---

## 概述

在 NAudio 中, 常用类型有 WaveIn, WaveOut, WaveStream, WaveFileWriter, WaveFileReader, AudioFileReader 以及接口: IWaveProvider, ISampleProvider, IWaveIn, IWavePlayer


1. WaveIn 表示波形输入, 继承了 IWaveIn, 例如麦克风输入, 或者计算机正在播放的音频流.
2. WaveOut 表示波形输出, 继承了 IWavePlayer, 用来播放波形音乐, 以 IWaveProvider 作为播放源播放音频, 通过拓展方法也支持以 ISampleProvider 作为播放源播放音频
3. WaveStream 表示波形流, 它继承了 IWaveProvider, 可以用来作为播放源.
4. WaveFileReader 继承了 WaveStream, 用来读取波形文件
5. WaveFileWriter 继承了Stream, 用来写入文件, 常用于保存音频录制的数据.
6. AudioFileReader 通用的音频文件读取器, 可以读取波形文件, 也可以读取其他类型的音频文件例如 Aiff, MP3
7. IWaveProvider 波形提供者, 上面已经提到, 是音频播放的提供者, 通过拓展方法可以转换为 ISampleProvider
8. ISampleProvider 采样提供者, 上面已经提到, 通过拓展方法可以作为 WaveOut 的播放源


## 播放音频


常用的播放音频方式有两种, 播放波形音乐, 以及播放 MP3 音乐


1. 播放波形音乐:

   ```csharp
   // NAudio 中, 通过 WaveFileReader 来读取波形数据, 在实例化时, 你可以指定文件名或者是输入流, 这意味着你可以读取内存流中的音频数据
   // 但是需要注意的是, 不可以读取来自网络流的音频, 因为网络流不可以进行 Seek 操作.
   
   // 此处, 假设 ms 为一个 MemoryStream, 内存有音频数据.
   WaveFileReader reader = new WaveFileReader(ms);
   WaveOut wout = new WaveOut();
   wout.Init(reader);             // 通过 IWaveProvider 为音频输出初始化
   wout.Play();                   // 至此, wout 将从指定的 reader 中提供的数据进行播放
   ```

2. 播放 MP3 音乐:

   ```csharp
   // 播放 MP3 音乐其实与播放波形音乐没有太大区别, 只不过将 WaveFileReader 换成了 Mp3FileReader 罢了
   // 另外, 也可以使用通用的 Reader, MediaFoundationReader, 它既可以读取波形音乐, 也可以读取 MP3
   
   // 此处, 假设 ms 为一个 MemoryStream, 内存有音频数据.
   Mp3FileReader reader = new Mp3FileReader(ms);
   WaveOut wout = new WaveOut();
   wout.Init(reader);
   wout.Play();
   ```


## 音频录制


1. 录制麦克风输入

   ```csharp
   // 借助 WaveIn 类, 我们可以轻易的捕获麦克风输入, 在每一次录制到数据时, 将数据写入到文件或其他流, 这就实现了保存录音
   // 在保存波形文件时需要借助 WaveFileWriter, 当然, 如果你想保存为其他格式, 也可以使用其它的 Writer, 例如 CurWaveFileWriter 以及
   // AiffFileWriter, 美中不足的是没有直接写入到 MP3 的 FileWriter
   // 需要注意的是, 如果你是用的桌面程序, 那么你可以直接使用 WaveIn, 其回调基于 Windows 消息, 所以无法在控制台应用中使用 WaveIn
   // 如果要在控制台应用中实现录音, 只需要使用 WaveInEvent, 它的回调基于事件而不是 Windows 消息, 所以可以通用
   
   WaveIn cap = new WaveIn();   // cap, capture
   WaveFileWriter writer = new WaveFileWriter();
   cap.DataAvailable += (s, args) => writer.Write(args.Buffer, 0, args.BytesRecorded);    // 订阅事件
   cap.StartRecording();   // 开始录制
   
   // 结束录制时:
   cap.StopRecording();    // 停止录制
   writer.Close();         // 关闭 FileWriter, 保存数据
   
   // 另外, 除了使用 WaveIn, 你还可以使用 WasapiCapture, 它与 WaveIn 的使用方式是一致的, 可以用来录制麦克风
   // Wasapi 全称 Windows Audio Session Application Programming Interface (Windows音频会话应用编程接口)
   // 具体 WaveIn, WaveInEvent, WasapiCapture 的性能, 笔者还没有测试过, 但估计不会有太大差异.
   // 提示: WasapiCapture 和 WasapiLoopbackCapture 位于 NAudio.Wave 命名空间下
   ```

2. 录制声卡输出

   ```csharp
   // 录制声卡输出, 也就是录制计算机正在播放的声音, 借助 WasapiLoopbackCapture 即可简单实现, 使用方式与 WasapiCapture 无异
   
   WasapiLoopbackCapture cap = new WasapiLoopbackCapture();
   WaveFileWriter writer = new WaveFileWriter();
   cap.DataAvailable += (s, args) => writer.Write(args.Buffer, 0, args.BytesRecorded);
   cap.StartRecording();
   ```


## 高级应用


1. 获取计算机实时播放音量大小

   ```csharp
   // 其实这个是基于刚刚的录制声卡输出的, 录制时的回调中, Buffer, BytesRecorded 指定了此次录制的数据 (缓冲区和数据长度)
   // 而这些数据, 其实是计算机对声音的采样(Sample), 具体的采样格式可以查看 WasapiLoopbackCapture 实例的 WaveForamt
   // 波形格式中的 Encoding 与 BitsPerSample 是我们所需要的. 一般默认的 Encoding 是 IeeeFloat, 也就是每一个采样都是
   // 一个浮点数, 而 BitsPerSample 也就是 32 了. 通过 BitConverter.ToSingle() 我们可以从缓冲区中取得浮点数
   // 遍历, 每 32 位一个浮点数, 最终取最大值, 这就是我们所需要的音量了
   
   float volume;
   WasapiLoopbackCapture cap = new WasapiLoopbackCapture();
   cap.DataAvailable += (s, args) => volume = Enumerable
                                        	       .Range(0, args.BytesRecorded / 4)                         // 每一个采样的位置
                                        	       .Select(i => BitConverter.ToSingle(args.Buffer, i * 4))   // 获取每一个采样
                                        	       .Aggregate((v1, v2) => v1 > v2 ? v1 : v2);                // 找到值最大的采样
   ```

2. 实现音乐可视化
   完整版 NAudio 实现音乐可视化: [[C#] 使用 NAudio 实现音频可视化](https://blog.csdn.net/m0_46555380/article/details/116573323?spm=1001.2014.3001.5501)
   ```csharp
   // 既然我们已经知道了, 那些数据都是一个个的采样, 自然也可以通过它们来绘制频谱, 只需要进行快速傅里叶变换即可
   // 而且有意思的是, NAudio 也为我们准备好了快速傅里叶变换的方法, 位于 NAudio.Dsp 命名空间下
   // 提示: 进行傅里叶变换所需要的复数(Complex)类也位于 NAudio.Dsp 命名空间, 它有两个字段, X(实部) 与 Y(虚部)
   // 下面给出在 IeeeFloat 格式下的音乐可视化的简单示例:
   WasapiLoopbackCapture cap = new WasapiLoopbackCapture();
   cap.DataAvailable += (s, args) =>
   {
       float[] samples = Enumerable
                             .Range(0, args.BytesRecorded / 4)
                             .Select(i => BitConverter.ToSingle(args.Buffer, i * 4))
                             .ToArray();   // 获取采样
       
       int log = (int)Math.Ceiling(Math.Log(samples.Length, 2));
       float[] filledSamples = new float[(int)Math.Pow(2, log)];
       Array.Copy(samples, filledSamples, samples.Length);   // 填充数据
       
       int sampleRate = (s as WasapiLoopbackCapture).WaveFormat.SampleRate;    // 获取采样率
       Complex[] complexSrc = filledSamples.Select((v, i) =>
       {
           double deg = i / (double)sampleRate * Math.PI * 2;                  // 获取当前采样率在圆上对应的角度 (弧度制)
           return new Complex()
           {
               X = (float)(Math.Cos(deg) * v),
               Y = (float)(Math.Sin(deg) * v)
           };
       }).ToArray();                                         // 将采样转换为对应的复数 (缠绕到圆)
       
       FastFourierTransform.FFT(false, log, complexSrc);     // 进行傅里叶变换
       double[] result = complexSrc.Select(v => Math.Sqrt(v.X * v.X + v.Y * v.Y)).ToArray();    // 取得结果
   };
   ```

3. 音频格式转换

   ```csharp
   // 对于 Wave, CueWave, Aiff, 这些格式都有其对应的 FileWriter, 我们可以直接调用其 Writer 的 Create***File 来
   // 从 IWaveProvider 创建对应格式的文件. 对于 MP3 这类没有 FileWriter 的格式, 可以调用 MediaFoundationEncoder
   
   // 例如一个文件, "./Disconnected.mp3", 我们要将它转换为 wav 格式, 只需要使用下面的代码, CurWave 与 Aiff 同理
   using (Mp3FileReader reader = new Mp3FileReader("./Disconnected.mp3"))
   	WaveFileWriter.CreateWaveFile("./Disconnected.wav", reader);
   
   // 从 IWaveProvider 创建 MP3 文件, 假如一个 WaveFileReader 为 src
   MediaFoundationEncoder.EncodeToMp3(src, "./NewMp3.mp3");
   ```


## 提示


对于刚刚所说的音频录制, 采样的格式有一点需要注意, 将数据转换为一个 float 数组后, 其中还需要区分音频通道, 例如一般音乐是双通道, WaveFormat 的 Channel 为 2, 那么在 float 数组中, 每两个采样为一组, 一组采样中每一个采样都是一个通道在当前时间内的采样.


以双通道距离, 下图中, 采样数据中每一个圆圈都表示一个 float 值, 那么每两个采样时间点相同, 而各个通道的采样就是每一组中每一个采样 


![image](images/d150eb7b73e62b2aed07a68442f51525.png)


所以对于我们刚刚进行的音乐可视化, 严格意义上来讲, 还需要区分通道


## 示例


本文提到的部分内容在 [github.com/SlimeNull/AudioTest](https://github.com/SlimeNull/AudioTest) 仓库中有示例, 例如音频可视化, 音频录制, 以及其他零星的示例


---


如有错误, 还请指出
