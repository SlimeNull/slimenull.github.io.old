---
title: '[C#] 就让这张图片来揭露你的本性吧! 老绅士.'
slug: '[CSharp]就让这张图片来揭露你的本性吧,老绅士.'
date: 2021-02-21T23:08:37+08:00
tags:
  - dotnet
  - csharp
  - csharp
  - graphics
  - gdi/gdi+
categories:
  - dotnet
  - 控制台
  - Windows
description: '淦!看到上面那幅图了吗? 放大, 放大, 看到了吗? 说的就是你 (滑稽 '
---

![qwq](images/20210212012512552.jpg)
看到上面那幅图了吗? 放大, 放大, 看到了吗? 说的就是你 .


## 淦!

#### 1. 正常的:

简单的, 就是最开始那张图片了, 看起来还彳亍, 对吧


好吧, 这种东西, 实现起来也不难, 无非是在图片中写字. 写字你总会吧? 我直接贴 Graphics 元数据:

```csharp
namespace System.Drawing
{
    //
    // 摘要:
    //     封装一个 GDI+ 绘图图面。 此类不能被继承。
    public sealed class Graphics : MarshalByRefObject, IDeviceContext, IDisposable
    {
        //
        // 摘要:
        //     在指定位置并且用指定的 System.Drawing.Brush 和 System.Drawing.Font 对象绘制指定的文本字符串。
        //
        // 参数:
        //   s:
        //     要绘制的字符串。
        //
        //   font:
        //     System.Drawing.Font，它定义字符串的文本格式。
        //
        //   brush:
        //     System.Drawing.Brush，它确定所绘制文本的颜色和纹理。
        //
        //   point:
        //     System.Drawing.PointF 结构，它指定所绘制文本的左上角。
        //
        // 异常:
        //   T:System.ArgumentNullException:
        //     brush 为 null。 或 - s 为 null。
        public void DrawString(string s, Font font, Brush brush, PointF point);
    }
}
```

懂了吧? 当然本程序可没那么简单嘿嘿! 我可是还实现了类似于蒙版的东西! (好吧也不是很难)


##### 2. 难一点的:

![](images/20210220205921619.jpg)
看出有什么区别没? 没错! 你发现, 背景变成单色, 而字变成彩色的了!!! 当然 PS 用剪切蒙版很容易实现. 那么, 用 C# 该如何实现呢?


首先创建一个位图, 作为我们的临时空间, 背景是黑色, 然后用白色的笔在上面写字. 哦豁, 这不就是一张蒙版吗? 


我们的源图像跟这个蒙版图是一样大小的, 输出图像当然也同样大小, 然后遍历原图像的像素, 并且判断蒙版图像的颜色, 如果蒙版图像的颜色是白色, 则把这个像素拷贝到输出图像中. 于是于是! 就实现了画彩色的字! (好吧的确有点投机取巧的意味.)


##### 3. 简单点的:

![](images/20210221230415739.jpg)
哈哈哈, 每一个字, 都相当于原图的一个像素, 可不可以说是变相的字符画?


##### 完整的内容介绍:

```bash
Null.CharImg: 字符图像混合工具

Null.CharImg [ -{CharWidth | CWidth} 字符占宽 ]
             [ -{CharHeight | CHeight} 字符占高 ]
             [ -{MaxWidth | MWidth} 最大宽度 ]
             [ -{MaxHeight | MHeight} 最大高度 ]
             [ -{FontSize | FSize} 字体大小 ]
             [ -{String | Str} 字符串内容 ]
             [ -{CorrectRate | CRate} 颜色矫正程度]
             [ -{BackgroundColor | BgColor} 背景颜色 ]
             [ -{FontFamily | Font} 字体 ]
             [ /{FontBold | Bold} ]
             [ /{Opposite | Oppo} ]
             [ /{AutoCorrect | Correct}]
             [ /{AutoBackground | AutoBg}]
             [ /{HighDefinition | HD}]
             -{Output | Out} 输出路径
             *源文件

Null.CharImg /{Help | H | ?} 显示本帮助内容

      其中参数的概述如下:
             字符占宽, 指图像中, 一个字符独占的宽度, 默认8
             字符占高, 字符独占的高度, 与字符占宽决定了字符的间距, 默认16
             最大宽度, 指输出图像中, 横向最多含多少个字符
             最大高度, 指输出图像中, 纵向最多含多少个字符
             字体大小, 指字体大小, 单位是像素, 默认16
             字符串内容, 要显示在图像中的字符, 将逐个显示
             颜色矫正程度, 指定了自动颜色矫正的程度, 值域是0~100, 默认100
             背景颜色, 表示图像背景颜色, 使用十六进制代码表示
             输出路径, 表示输出图像的保存路径
             字体, 表示图像中字符所使用的字体, 默认是Sans Serif
             FontBold, 粗体, 表示是否使用粗体
             Opposite, 使背景与前景颜色反转
             AutoCorrect, 自动矫正颜色
             AutoBackground, 自动判断背景色
             HighDefinition, 高分辨率模式

      关于自动矫正颜色:
             自动矫正颜色旨在尝试调节前景色的色彩, 以做到前景与背景结合时, 颜色
             趋近于原图, 如果背景颜色指定了亮度较高的颜色, 例如白色, 这个功能会
             启到很好的作用, 如果你认为颜色矫正有些严重, 可以使用 CorrectRate
             参数来指定自动调节的力度, 尝试调节为80, 甚至更低的值来适应.

      关于自动判断背景色
             当启动该功能时, 用户设置的颜色作废, 程序会自动计算整个原图像的平均色
             作为输出图像的背景色

      关于高分辨率模式:
             当启用高分辨率时, 图像中的一个字符不再单单是一种颜色, 而是使用彩色,
             以使输出图像更能趋近于原图的形状与颜色, 同时Opposite也是受支持的

      关于程序:
             作者: 诺尔, Null; 擅长:摸鱼
             Github仓库: https://github.com/SlimeNull/Null.CharImg
```


> 如果有啥感到迷惑的, 在下方留言噢~
