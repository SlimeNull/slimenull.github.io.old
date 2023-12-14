---
title: '[C#] 使用 NAudio 实现音频可视化'
date: 2021-05-09 23:49:18
tags:
  - 可视化
  - .net
  - c#
categories:
  - 笔记
  - .NET
  - 桌面程序
description: '预览:捕捉声卡输出:实现音频可视化, 第一步就是获得音频采样, 这里我们选择使用计算机正在播放的音频作为采样源进行处理:NAudio 中, 可以借助 WasapiLoopbackCapture 来进行捕捉:WasapiLoopbackCapture cap = new WasapiLoopbackCapture();cap.DataAvailable += (sender, e) =>      // 录制数据可用时触发此事件, 参数中包含音频数据{    float[] allSam'
---

## 预览:


最初版本:
![preview](images/20210509234816552.gif)
更新:
<a href="https://sm.ms/image/TCABEJvFoDMrnaf" target="_blank"><img src="https://i.loli.net/2021/06/25/TCABEJvFoDMrnaf.gif" ></a>


再次更新 (这次优化了算法):


[video(video-meXn8OHU-1667173967679)(type-bilibili)(url-https://player.bilibili.com/player.html?aid=902039129)(image-https://img-blog.csdnimg.cn/img_convert/86ebcb2e350f1bce9383f671951f1fb7.jpeg)(title-[演示] C# NAudio Direct2D 音频可视化)]



## 捕捉声卡输出:


实现音频可视化, 第一步就是获得音频采样, 这里我们选择使用计算机正在播放的音频作为采样源进行处理:


NAudio 中, 可以借助 WasapiLoopbackCapture 来进行捕捉:


```csharp
WasapiLoopbackCapture cap = new WasapiLoopbackCapture();
cap.DataAvailable += (sender, e) =>      // 录制数据可用时触发此事件, 参数中包含音频数据
{
    float[] allSamples = Enumerable      // 提取数据中的采样
        .Range(0, e.BytesRecorded / 4)   // 除以四是因为, 缓冲区内每 4 个字节构成一个浮点数, 一个浮点数是一个采样
        .Select(i => BitConverter.ToSingle(e.Buffer, i * 4))  // 转换为 float
        .ToArray();    // 转换为数组
    // 获取采样后, 在这里进行详细处理
}
cap.StartRecording();   // 开始录制
```




## 分离左右通道:


获取完采样后, 我们还需要对采样进行一点小处理, 因为捕获的数据是分通道的, 一般是左右声道:


```csharp
// 设定我们已经将刚刚的采样保存到了变量 AllSamples 中, 类型为 float[]
int channelCount = cap.WaveFormat.Channels;   // WasapiLoopbackCapture 的 WaveFormat 指定了当前声音的波形格式, 其中包含就通道数
float[][] channelSamples = Enumerable
    .Range(0, channelCount)
    .Select(channel => Enumerable
        .Range(0, AllSamples.Length / channelCount)
        .Select(i => AllSamples[channel + i * channelCount])
        .ToArray())
    .ToArray();
```




## 取通道平均值


将采样分为一个个通道的采样后, 我们可以将其合并, 取平均值, 以便于绘制:


```csharp
// 设定我们已经将分开了的采样保存到了变量 ChannelSamples 中, 类型为 float[][]
// 例如通道数为2, 那么左声道的采样为 ChannelSamples[0], 右声道为 ChannelSamples[1]
float[] averageSamples = Enumerable
    .Range(0, AllSamples.Length / channelCount)
    .Select(index => Enumerable
        .Range(0, channelCount)
        .Select(channel => ChannelSamples[channel][index])
        .Average())
    .ToArray();
```




## 绘制时域图象:


处理刚刚的采样后, 你可以直接将其作为数据绘制到窗口中, 这即是时域图象, 这里使用最简单的折线绘制.


```csharp
// 设定 g 为窗口的 Graphics 对象, windowHeight 为窗口的显示区域高度
// 设定通道采样平均值为 AverageSamples, 类型为 float[]
Point[] points = AverageSamples
    .Select((v, i) => new Point(i, windowHeight - v))
    .ToArray();   // 将数据转换为一个个的坐标点
g.DrawLines(Pens.Black, points);   // 连接这些点, 画线
```




## 傅里叶变换:


NAudio 中还提供了快速傅里叶变换的方法, 通过傅里叶变换, 可以将时域数据转换为频域数据, 也就是我们所说的频谱


```csharp
// 我们将对 AverageSamples 进行傅里叶变换, 得到一个复数数组

// 因为对于快速傅里叶变换算法, 需要数据长度为 2 的 n 次方, 这里进行
float log = Math.Ceiling(Math.Log(AverageSamples.Length, 2));   // 取对数并向上取整
int newLen = (int)Math.Pow(2, log);                             // 计算新长度
float[] filledSamples = new float[newLen];
Array.Copy(AverageSamples, filledSamples, AverageSamples.Length);   // 拷贝到新数组
Complex[] complexSrc = filledSamples
    .Select(v => new Complex(){ X = v })        // 将采样转换为复数
    .ToArray();
FastFourierTransform(false, log, complexSrc);   // 进行傅里叶变换

// 变换之后, complexSrc 则已经被处理过, 其中存储了频域信息
```




## 分析频域信息:


对于傅里叶变换的频域信息, 需要稍加处理才可以方便的使用, 首先是提取有用的信息:


```csharp
// NAudio 的傅里叶变换结果中, 似乎不存在直流分量(这使我们的处理更加方便了), 但它也是有共轭什么的(也就是数据左右对称, 只有一半是有用的)
// 仍然使用刚刚的 complexSrc 作为变换结果, 它的类型是 Complex[]

Complex[] halfData = complexSrc
    .Take(complexSrc.Length / 2)
    .ToArray();    // 一半的数据
float[] dftData = halfData
    .Select(v => Math.Sqrt(v.X * v.X + v.Y * v.Y))  // 取复数的模
    .ToArray();    // 将复数结果转换为我们所需要的频率幅度

// 其实, 到这里你完全可以把这些数据绘制到窗口上, 这已经算是频域图象了, 但是对于音乐可视化来讲, 某些频率的数据我们完全不需要
// 例如 10000Hz 的频率, 我们完全没必要去绘制它, 取 最小频率 ~ 2500Hz 足矣
// 对于变换结果, 每两个数据之间所差的频率计算公式为 采样率/采样数, 那么我们要取的个数也可以由 2500 / (采样率 / 采样数) 来得出
int count = 2500 / (cap.WaveFormat.SampleRate / filledSamples.Length);
float[] finalData = dftData.Take(count).ToArray();
```




## 绘制频域图象:


得到上面分析后的 finalData 后, 我们就可以直接绘制出来了, 这次使用柔和的曲线绘制


```csharp
// 设定 g 为窗口的 Graphics 对象, height 为窗口高度
PointF[] points = finalData
    .Select((v, i) => new PointF(i, height - v))
    .ToArray();
g.DrawCurve(Pens.Purple, points);    // Graphics 可以直接绘制曲线
```




## 更优的绘制:


上面的时域和频域图象, 我们都是一股脑的将数据的索引作为 X 坐标, 窗口高度减去数据值作为 Y 坐标, 有两个突出的问题:


1. 数据可能无法填满窗口的宽度或者超出窗口的宽度范围
2. 数据太大时, 也会导致绘制的线条超出窗口高度


第一个问题好解决, 直接使索引所占数据长度的百分比恰好等于 X 坐标相对于窗口宽度的百分比即可:
$$
x = index \div dataLength * windowWidth
$$
对于第二个问题, 有两个解决方案, 一是直接为数据加权重, 例如统一乘 0.5, 使数据减小一节, 二就是套一个函数, 例如 log 函数, 毕竟 log 函数在较高自变量的情况下, 因变量的变化趋势越来越小, 我们只需要对这个 log 函数进行稍加处理, 就可以直接应用到数据变换数据上, 使其不超出窗口绘图区域


另外, 我们也可以平滑频谱显示(指动画变换), 它的原理大概是这样: 


> 1. 例如这次进行傅里叶变换的结果是: `{0, 100, 50}`, 
>
> 2. 下一次傅里叶变换的结果是: `{100, 0, 0}`, 
>
> 3. 可以得出, 增量为: `{100, -100, -50}`, 
>
>    在更新变换结果时, 我们不再直接将新的结果替换旧的结果, 而是在旧的结果的基础上, 加上增量×权重
>
> 4. 例如权重是 `0.5` 时, 那么实际增量是: `{50, -50, -25}`, 
>
> 5. 那么实际新的值是: `{50, 50, 25}`, 
>
> 6. 如果下一次变换的结果还是 `{100, 0, 0}`, 那我们再次从 `{50, 50, 25}` 向新值逼近, 权重仍然是 `0.5`, 那么实际增量是: `{25, -25, -12.5}`, 
>
> 注意到了吗? 这次的增量是上次增量的一半, 这正好是一个减速运动, 而且新值与旧值的差越大, 变化的就越快, 而它们会不断重合, 因而速度不断变慢, 形成减速运动的频谱图.




## 更多内容:


更多关于 NAudio 的使用, 可以看这篇文章: [[C#] NAudio 的各种常见使用方式 播放 录制 转码 音频可视化](https://blog.csdn.net/m0_46555380/article/details/116460477)




## 项目已开源:


关于本文章涉及的大部分内容, 均在 [github.com/SlimeNull/AudioTest](https://github.com/SlimeNull/AudioTest) 仓库中的 Null.AudioVisualizer 项目中有写. (注释妥当了)


如果是之后更新过的, 优化了算法的,可以在 [github.com/SlimeNull/AudioVisualizer](https://github.com/SlimeNull/AudioVisualizer) 仓库查看源代码. (注释妥当了)



---


其实音频可视化我老早就想做了, 但是本人数学不是非常的好, 不过最后总算是坚持下来了, 弄出来了啊, 心情老激动了


<div align="right">求个赞, 求个评论~</div>
