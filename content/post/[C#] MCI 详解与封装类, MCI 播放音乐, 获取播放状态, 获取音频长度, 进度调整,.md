---
title: '[C#] MCI 详解与封装类, MCI 播放音乐, 获取播放状态, 获取音频长度, 进度调整,'
slug: '[CSharp]MCI详解与封装类,MCI播放音乐,获取播放状态,获取音频长度,进度调整,'
date: 2021-02-11 10:21:11
tags:
  - dotnet
  - mci
  - win32
  - windows
  - winapi
categories:
  - 笔记
  - Windows
  - 成长记录
description: '淦!琢磨了一晚上啊, 总算有些眉目了.首先, MCI的全称是Multimedia Control Interface, 即多媒体控制接口, 通过它, 我们可以做到播放音频视频, 甚至录制音频, 虽然古老, 但是真的强大.注意, 文章较官方文档有不少删减, 如果看标准内容, 还是看官方文档比较好.MCI Command Strings 官方文档: Microsoft Command Strings - Win32 app | Microsoft Docs哦对了, 文档是纯英文哦~理论基础:'
---


## 淦!

琢磨了一晚上啊, 总算有些眉目了. <sub>(所以我还是一个笨蛋啊, 明明这么简单的东西却花费了这么长时间</sub>


首先, MCI的全称是Multimedia Control Interface, 即多媒体控制接口, 通过它, 我们可以做到播放音频视频, 甚至录制音频, 虽然古老, 但是真的强大.


注意, 文章较官方文档有不少删减, 如果看标准内容, 还是看官方文档比较好.


> MCI Command Strings 官方文档: [Microsoft Command Strings - Win32 app | Microsoft Docs](https://docs.microsoft.com/en-us/windows/win32/multimedia/mci-command-strings)
> 哦对了, 文档是纯英文哦~


## 理论基础:

1. MCI 不能与 C# 中的内存流打交道, 他只能播放文件.
2. MCI 支持很多格式, 包含: CD音频, 数字音频, MIDI音乐, 视频唱片(videodisc), VCR, 以及波形音频.
3. MCI 中, 被播放的音频文件被称作 **设备(Device)**
4. MCI 中, 支持巨多设置项, 包括播放进度, 音量大小, 声道开关, 如果你播放的是 MIDI, 支持的设置更多.


## 正式开始:

下面, 我们将以播放音频为例, 尽可能多的讲述 MCI 的相关知识, 文章在今后可能会继续更新.


示例文件在: C:\Users\Null\Desktop\Tutorial.wav

#### > 引用库:

- 最最开始, 无非是载入库.

1. 对于 C++, 需要引用 Windows.h 以及 Mmsystem.h 这两个头文件
2. 对于 C#, using System.Runtime.InteropServices; 以进行非托管库的调用.

#### > 执行指令:


- MCI 中, 有 3 种方式来执行 MCI 指令. 分别是: mciSendString, mciExecute, mciSendCommand, 它们均位于 winmm.dll 中.

#

- **函数原型:**
  ```c
  MCIERROR mciSendString(    // MCIERROR 是 无符号长整形数字
      LPCTSTR lpszCommand,
      LPTSTR  lpszReturnString,
      UINT    cchReturn,
      HANDLE  hwndCallback
  );
    
  BOOL mciExecute(
      LPCSTR pszCommand
  );

	MCIERROR mciSendCommand(
	    MCIDEVICEID IDDevice,    // 设备 ID, 通过另一个函数打开文件可以获得
	    UINT        uMsg,
	    DWORD_PTR   fdwCommand,
	    DWORD_PTR   dwParam
	);
    ```
- **C# 声明方式:** 
  ```csharp
  [DllImport("winmm.dll", EntryPoint = "mciSendString", CharSet = CharSet.Unicode)]
  extern static ulong MciSendString(string command, string buffer, int bufferSize, IntPtr callback);
  
  [DllImport("winmm.dll", EntryPoint = "mciExecute", CharSet = CharSet.Unicode)]
  extern static bool MciExecute(string command);
  
  [DllImport("winmm.dll", EntryPoint = "mciSendCommand", CharSet = CharSet.Unicode)]
  extern static ulong MciSendCommand(uint deviceId, uint uMsg, IntPtr fdwCommand, IntPtr dwParam);
  ```
   
- **简略说明:**
  > mciSendString: 最常用的方法, 通过字符串来表示一个指令, 会返回错误码, 不会有弹窗警告
  > mciExecute: 调试时较常用的方法, 在执行时若有未完善的地方, 会弹窗警告, 也因为如此, 程序的发布版本不会使用这个(影响使用体验)
  > mciSendCommand: 很少用, 通过指令的ID来表示指令, 会返回错误码, 不会有弹窗警告
  > &nbsp;
  > 某些 MCI 指令具有返回值, 例如获取播放状态, 这些指令不能通过mciExecute执行.


#

- 文章只会通过 mciSendString 来介绍 MCI 哟



#### > 打开文件:

- 对于播放音频, 首要的第一件事, 肯定就是打开文件并将其载入到内存了. 不过有一点很重要, 就是 MCI 指令只支持短路径(ShortPath), 所以在拿到文件路径后, 我们得将其转换为短路径.
- 如果不对路径进行转换, 那么某些名字长度大于8的文件(准确来说是路径中任何一部分长度大于8)的将无法播放
  > 关于短路径与长路径: [windows系统下的文件长名和文件短名](https://blog.csdn.net/zfs2008zfs/article/details/51154873)
  > 短路径与长路径的转换: [[C#/C/C++] GetShortPathName 详解, 长路径转换为短路径](https://blog.csdn.net/m0_46555380/article/details/113765617)
- MCI 指令中, 通过 open 来打开一个文件, 并且在末尾还可以使用 "alias 别名" 来为这个已打开的文件起一个别名.
- 下面是两个示例:
  ```csharp
  // 文件是 C:\Users\Null\Desktop\Tutorial.wav
  // 转换为短路径是 C:\Users\Null\Desktop\Tut~1.wav
  mciSendString(@"open C:\Users\Null\Desktop\Tut~1.wav", null, 0, IntPtr.Zero);
  mciSendString(@"open C:\Users\Null\Desktop\Tut~1.wav alias tutorial", null, 0, IntPtr.Zero);
  ```
  在第一的 mciSendString 中, 很清晰明了, 打开了一个文件, 而第二个中, 我们还加了一个alias, 即, 别名, MCI支持为打开的文件起一个别名, 并且推荐这么做.
  第二个种, 我们为它起的别名是 tutorial.


#### > 播放音频:

- 播放音频的 MCI 指令是 play, 直接 "play 设备"
- 示例:
  ```csharp
  mciSendString(@"play C:\Users\Null\Desktop\Tut~1.wav", null, 0, IntPtr.Zero);
  mciSendString(@"play tutorial", null, 0, IntPtr.Zero);
  ```
  如果你没有为文件指定别名, 那么在使用 play 指令时, 只能指定短路径
  如果你为文件指定了别名, 直接play加上别名即可播放这个文件.


#### > 重复播放音频:

- 这里, 用到了参数, 就像alias一样, 可选. 重复播放还是使用的play指令.
- 示例:
  ```csharp
  mciSendString(@"play C:\Users\Null\Desktop\Tut~1.wav repeat", null, 0, IntPtr.Zero);
  mciSendString(@"play tutorial repeat", null, 0, IntPtr.Zero);
  ```
  设备名如旧, 可以直接指定短路径, 也可以指定别名, 而想做到重复播放, 只需要在最后指定repeat


#### > 同步播放音频:

- 同样是参数, 这里是wait, 支持wait参数的指令可以同步执行, 例如play指令
- 示例:
  ```csharp
  mciSendString(@"play C:\Users\Null\Desktop\Tut~1.wav wait", null, 0, IntPtr.Zero);
  mciSendString(@"play tutorial wait", null, 0, IntPtr.Zero);
  ```
  执行后, 将会阻塞当前现成, 直至播放结束.


#### > 暂停播放:

- 暂停, pause, 用法很简单, 同样是 "pause 设备"
- 示例:
  ```csharp
  mciSendString(@"pause C:\Users\Null\Desktop\Tut~1.wav", null, 0, IntPtr.Zero);
  mciSendString(@"pause tutorial", null, 0, IntPtr.Zero);
  ```
  执行后, 正在播放的音频就会暂停.
    

#### > 恢复播放:

- 恢复, resume, 语法是 "resume 设备"
- 示例:
  ```csharp
  mciSendString(@"pause C:\Users\Null\Desktop\Tut~1.wav", null, 0, IntPtr.Zero);
  mciSendString(@"pause tutorial", null, 0, IntPtr.Zero);
  ```
  执行后, 暂停播放的音频就会恢复.


#### > 关闭文件:

- 关闭, close, 语法是: "close 设备"
- 示例:
  ```csharp
  mciSendString(@"close C:\Users\Null\Desktop\Tut~1.wav", null, 0, IntPtr.Zero);
  mciSendString(@"close tutorial", null, 0, IntPtr.Zero);
  ```
  执行后, 文件会被关闭. 如果音频正在播放, 则会停止.

#### > 改变播放位置:

- seek 指令, 这个指令比较复杂哦. 语法如下:
  > seek *device* to *position*
  > seek *device* to start
  > seek *device* to end
- 示例:
  ```csharp
  mciSendString(@"seek tutorial to 1000", null, 0, IntPtr.Zero);
  mciSendString(@"seek tutorial to start", null, 0, IntPtr.Zero);
  mciSendString(@"seek tutorial to end", null, 0, IntPtr.Zero);
  ```
  短路径肯定是能用的哦, 我只是懒得写了, 至于 "seek *device* to *position*" 的用法,  其中position默认是ms为单位的整数时间哦, 也就是说, 1000代表1s.
- seek 的单位是可以调整的, 继续看哦


#### > 设置相关:

- 设置, set, 这个支持的就更多了
  > **以下是适用于 CD Audio 的语法 :**
  > set *device* time format milliseconds
  > set *device* time format msf
  > set *device* time format tmsf

  > **以下是适用于 Wave Audio 的语法 :**
  > set *device* any input
  > set *device* any output
  > set *device* channels *channel_count*
  > set *device* format tag pcm
  > set *device* format tag tag
  > set *device* input *integer*
  > set *device* output *integer*
  > set *device* time format bytes
  > set *device* time format milliseconds

  | 选项                     | 描述                                                         |
  | ------------------------ | ------------------------------------------------------------ |
  | time format milliseconds | 将时间格式设置为毫秒, 所有使用position值的指令都将采用毫秒作为单位, 你可以将milliseconds缩写为ms. 对于音序器设备, |
  | time format msf          | 设置时间格式到 分钟, 妙, 以及帧. 所有使用position值的指令都见采用MSF格式(CD音频的默认格式), 请将MSF值指定为 mm:ss:ff 的格式, mm是分钟, ss是秒, ff是帧. 如果一个字段以及后面的字段都是0, 你可以忽略掉它. 例如, 3, 3:0, 3:0:0 都是表示3分钟的正确方式. MSF字段有以下最大值, 分钟:99, 秒:59, 帧: 74. |
  | time format tmsf         | 将时间格式设置为 音轨, 分钟, 秒, 以及帧. 所有使用position值的指令都见采用TMSF格式, 额, 与上面的一样, 只不过多了个音轨. 音轨的最大值是99, 分钟, 秒, 帧的最大值与MSF格式一致. |
  | any input                | 当录制时, 使用所有支持当前格式的输入, 这是默认设置           |
  | any output               | 当播放时, 使用所有支持当前格式的输出, 这是默认的             |
  | time format bytes        | 在 PCM 文件格式中, 设置时间格式(单位)为字节, 指定这个指令后, 所有position信息都将被指定为字节格式 |


#### > 状态信息:


- 状态, status, 语法: "status *device* *option*", 返回到 mciSendString 参数指定的字符串缓冲区

  > **适用于音频的常用语法**
  >
  > status *device* position
  >
  > status *device* length
  >
  > status *device* mode
  >
  > status *device* time format
  
  | 选项        | 描述                                                         |
  | ----------- | ------------------------------------------------------------ |
  | position    | 获取目前播放的位置单位与时间格式统一                         |
  | length      | 获取当前播放音频的长度 单位与时间格式统一                    |
  | mode        | 获取播放状态, 返回的值是以下值之一: stopped / playing / paused |
  | time format | 获取当前的时间格式                                           |
  
  
  
  ```csharp
  string buffer = new string('\0', 256);       // 分配一个长度的字符串用来存放返回值
  mciSendString("status tutorial position", buffer, 256, IntPtr.Zero);   // 调用
  Console.WriteLine(buffer.TrimEnd('\0'));     // 打印返回值, TrimEnd的原因字符串的是长度是256, 函数没有使用的部分仍然是 \0 字符.
  ```


#### > 音频设置


- 设置音频, setaudio, 语法 "setaudio *device* *option*"

  > **常用语法:**
  >
  > setaudio *device* left volume to *factor*
  >
  > setaudio *device* right volume to *factor*
  >
  > setaudio *device* volume to *factor*

  | 选项                          | 描述                         |
  | ----------------------------- | ---------------------------- |
  | left/right volume to *factor* | 将指定声道的音量设置为指定值 |
  | volume to *factor*            | 将音量设置为指定值           |


## 错误码:


- 下面是sendMciString会返回的错误码以及描述(对名称翻译, 然后稍加校正), 哦对了, 返回 0 代表无错误哦

  | 错误码 | 名称 | 描述 |
  | ------ | ---- | ---- |
  | 257 | MCIERR_INVALID_DEVICE_ID | 无效设备 ID |
  | 259 | MCIERR_UNRECOGNIZED_KEYWORD | 未识别关键字 |
  | 261 | MCIERR_UNRECOGNIZED_COMMAND | 未识别的命令 |
  | 262 | MCIERR_HARDWARE | 硬件 |
  | 263 | MCIERR_INVALID_DEVICE_NAME | 无效的设备名称 |
  | 264 | MCIERR_OUT_OF_MEMORY | 内存不足 |
  | 265 | MCIERR_DEVICE_OPEN | 设备打开 |
  | 266 | MCIERR_CANNOT_LOAD_DRIVER | 无法加载驱动程序 |
  | 267 | MCIERR_MISSING_COMMAND_STRING | 缺少命令字符串 |
  | 268 | MCIERR_PARAM_OVERFLOW | 参数溢出 |
  | 269 | MCIERR_MISSING_STRING_ARGUMENT | 缺少字符串参数 |
  | 270 | MCIERR_BAD_INTEGER | 坏整数 |
  | 271 | MCIERR_PARSER_INTERNAL | 分析器内部 (估计是这个API内部对指令分析时出现的错误) |
  | 272 | MCIERR_DRIVER_INTERNAL | 驱动程序内部 |
  | 273 | MCIERR_MISSING_PARAMETER | 缺少参数 |
  | 274 | MCIERR_UNSUPPORTED_FUNCTION | 不支持的功能 |
  | 275 | MCIERR_FILE_NOT_FOUND | 未找到文件 |
  | 276 | MCIERR_DEVICE_NOT_READY | 设备未就绪 |
  | 277 | MCIERR_INTERNAL | 内部 |
  | 278 | MCIERR_DRIVER | 驱动器 |
  | 279 | MCIERR_CANNOT_USE_ALL | 不能全部使用 |
  | 280 | MCIERR_MULTIPLE | 多个 |
  | 281 | MCIERR_EXTENSION_NOT_FOUND | 未找到扩展 |
  | 282 | MCIERR_OUTOFRANGE | 超出范围 |
  | 284 | MCIERR_FLAGS_NOT_COMPATIBLE | 标志不兼容 |
  | 286 | MCIERR_FILE_NOT_SAVED | 文件未保存 |
  | 287 | MCIERR_DEVICE_TYPE_REQUIRED | 需要设备类型 |
  | 288 | MCIERR_DEVICE_LOCKED | 设备已锁定 |
  | 289 | MCIERR_DUPLICATE_ALIAS | 重复别名 |
  | 290 | MCIERR_BAD_CONSTANT | 坏常量 |
  | 291 | MCIERR_MUST_USE_SHAREABLE | 必须使用可共享 |
  | 292 | MCIERR_MISSING_DEVICE_NAME | 缺少设备名称 |
  | 293 | MCIERR_BAD_TIME_FORMAT | 错误的时间格式 |
  | 294 | MCIERR_NO_CLOSING_QUOTE | 没有关闭中的引用 |
  | 295 | MCIERR_DUPLICATE_FLAGS | 重复标志 |
  | 296 | MCIERR_INVALID_FILE | 无效文件 |
  | 297 | MCIERR_NULL_PARAMETER_BLOCK | 空参数块 |
  | 298 | MCIERR_UNNAMED_RESOURCE | 未命名的资源 |
  | 299 | MCIERR_NEW_REQUIRES_ALIAS | 新需要别名 |
  | 300 | MCIERR_NOTIFY_ON_AUTO_OPEN | 自动打开时通知 |
  | 301 | MCIERR_NO_ELEMENT_ALLOWED | 不允许任何元素 |
  | 302 | MCIERR_NONAPPLICABLE_FUNCTION | 不可应用功能 |
  | 303 | MCIERR_ILLEGAL_FOR_AUTO_OPEN | 非法自动打开 |
  | 304 | MCIERR_FILENAME_REQUIRED | 需要文件名 |
  | 305 | MCIERR_EXTRA_CHARACTERS | 额外字符 (可能是指多出了一些不需要的字符) |
  | 306 | MCIERR_DEVICE_NOT_INSTALLED | 未安装设备 |
  | 307 | MCIERR_GET_CD | 获取 CD |
  | 308 | MCIERR_SET_CD | 设置 CD |
  | 309 | MCIERR_SET_DRIVE | 设置驱动器 |
  | 310 | MCIERR_DEVICE_LENGTH | 设备长度 |
  | 311 | MCIERR_DEVICE_ORD_LENGTH | 设备 ORD 长度 |
  | 312 | MCIERR_NO_INTEGER | 无整数 |
  | 320 | MCIERR_WAVE_OUTPUTSINUSE | 波输出 |
  | 321 | MCIERR_WAVE_SETOUTPUTINUSE | 波设置输出 |
  | 322 | MCIERR_WAVE_INPUTSINUSE | 波输入使用 |
  | 323 | MCIERR_WAVE_SETINPUTINUSE | 波设置 |
  | 324 | MCIERR_WAVE_OUTPUTUNSPECIFIED | 波输出未指定 |
  | 325 | MCIERR_WAVE_INPUTUNSPECIFIED | 波输入未指定 |
  | 326 | MCIERR_WAVE_OUTPUTSUNSUITABLE | 波输出可居住 |
  | 327 | MCIERR_WAVE_SETOUTPUTUNSUITABLE | 波设置通普通西装 |
  | 328 | MCIERR_WAVE_INPUTSUNSUITABLE | 波输入可居住 |
  | 329 | MCIERR_WAVE_SETINPUTUNSUITABLE | 波设置通图适合 |
  | 336 | MCIERR_SEQ_DIV_INCOMPATIBLE | Seq Div 不兼容 |
  | 337 | MCIERR_SEQ_PORT_INUSE | SEQ 端口 INUSE |
  | 338 | MCIERR_SEQ_PORT_NONEXISTENT | Seq 端口不存在 |
  | 339 | MCIERR_SEQ_PORT_MAPNODEVICE | Seq 端口地图无设备 |
  | 340 | MCIERR_SEQ_PORT_MISCERROR | SEQ 杂项错误 |
  | 341 | MCIERR_SEQ_TIMER | SEQ 定时器 |
  | 342 | MCIERR_SEQ_PORTUNSPECIFIED | SEQ 端口未指定 |
  | 343 | MCIERR_SEQ_NOMIDIPRESENT | SEQ 没有MIDI在场 |
  | 346 | MCIERR_NO_WINDOW | 无窗口 |
  | 347 | MCIERR_CREATEWINDOW | 创建窗口 |
  | 348 | MCIERR_FILE_READ | 文件读取 |
  | 349 | MCIERR_FILE_WRITE | 文件写入 |
  | 350 | MCIERR_NO_IDENTITY | 无标识 |


## 封装类:

> 去这里吧, 在我的另一篇文章中 [[C#] 音乐播放 3 种方式 Demo 与 MCI 音乐播放器封装类.](https://blog.csdn.net/m0_46555380/article/details/113788780) o(〃'▽'〃)o

## 附加内容:

- 下面是我的一些发现
  > 1. WinForm 程序使用 MCI 是可以打开 MP3 文件的, 但是如果是控制台程序, 就会出现错误, 错误码266,  "MCIERR_CANNOT_LOAD_DRIVER"
  > 2. MCI 的某些指令不能正常使用, 但其实并不是很影响, 例如, "set *device* audio *left/right* *off/off*", 无法正常使用.
  > 3. 音量控制我目前还是没弄成... 不过可以确认的是, 进度获取, 调整, 长度获取是没问题的, 有这些最基本的, 就差不多公用了呢





放一张我写文章时的照片吧 , 原文贴的哪都是 (笑

![手动翻译, 淦](images/20210209110959116.png)
