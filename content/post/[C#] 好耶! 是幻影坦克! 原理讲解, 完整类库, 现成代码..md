---
title: '[C#] 好耶! 是幻影坦克! 原理讲解, 完整类库, 现成代码.'
slug: '[CSharp]好耶,是幻影坦克,原理讲解,完整类库,现成代码.'
date: 2021-03-03 16:44:09
tags:
  - c#
  - csharp
  - .net
  - 图像处理
  - 幻影坦克
categories:
  - 类库
  - 图片处理
  - 算法
description: '啥是幻影坦克? 幻影坦克就是, 一张黑白图片, 在黑色背景下和白色背景下能够显示出不同的图像.首先, 我可以明确的告诉你, 它的原理就是控制像素的颜色和Alpha通道(不透明度), 来使显示的图像在不同背景下显示不同的颜色.最基本的, 就是, 一张半透明的黑色薄膜, 如果在黑色的纸上, 你什么也看不出来, 但如果在白色纸上, 你可以看见, 它是灰色.本文中, Alpha 统一拟定为 0 ~ 1 的浮点数, 像素亮度统一为 0 ~ 1 即像素’白的程度’, 例如纯白为1, 纯黑则0.注意:本项'
---

啥是幻影坦克? 幻影坦克就是, 一张黑白图片, 在黑色背景下和白色背景下能够显示出不同的图像.

![](images/20210303103019196.png)
首先, 我可以明确的告诉你, 它的原理就是控制像素的颜色和Alpha通道(不透明度), 来使显示的图像在不同背景下显示不同的颜色.


最基本的, 就是, 一张半透明的黑色薄膜, 如果在黑色的纸上, 你什么也看不出来, 但如果在白色纸上, 你可以看见, 它是灰色.


> 本文中, Alpha 统一拟定为 0 ~ 1 的浮点数, 像素亮度统一为 0 ~ 1 即像素'白的程度', 例如纯白为1, 纯黑则0.


## 注意:

本项目已经在 [github.com/SlimeNull/Null.PhantomTank](https://github.com/SlimeNull/Null.PhantomTank) 开源. 你可以直接 clone 下来以查看源码.


幻影坦克合成库, NullLib.PhantomTank 已经发布到 [nuget.org](https://www.nuget.org/packages/NullLib.PhantomTank/), 你可以直接在 VS 的 nuget 包管理器中直接安装本库


## 基本原理:

像素在某背景下最终显示出来的公式如下:

> 设: 这个图像的背景亮度为 $bc$, 这个像素的亮度为 $pc$, 不透明度为 $pa$, 则最终显示的颜色 $oc$ 就是:
> $$
> oc = pc  * pa + bc * (1 - pa)
> $$


理解起来也简单, 还拿刚刚的例子来讲, 例如一个纯黑的半透明薄膜, 那他肯定:

1. 只有一半的黑色能显示出来, 即 $pc * pa$
2. 而背景色, 也有一半的颜色能够透过来. 即: $bc * (1 - pa)$
3. 总的颜色加起来, 也就是 $pc * pa + bc * (1 - pa)$


## 幻影坦克:

而当一个像素为白色背景时, 能够显示出一个特定的颜色 $x$, 当黑色背景时, 显示出 $y$, 也可得出一个公式,


- 设: 颜色 $x$ 的亮度为 $xc$, 颜色 $y$ 的亮度为 $yc$, 这个像素的亮度为 $zx$, 不透明度为 $za$, 则满足:
$$
\begin{cases}
xc = za \times zc + (1 - za) \\
yc = za \times zc
\end{cases}
$$

- 稍微处理一下, 可得到下面的公式:
$$
\begin{cases}
xc = yc + 1 \times (1 - za) \\
yc = xc - 1 \times (1 - za) \\
za = -[(xc - yc) \div 1] + 1 \\
\end{cases}
\\ \downarrow \\
\begin{cases}
xc = yc + 1 - za \\
yc = xc + za - 1 \\
za = yc - xc + 1 \\
\end{cases}
$$

- 由于 $xc$, $yc$, $zc$, $za$ 都是小于等于1, 大于等于0的值, 所以:
$$
\begin{cases}
1 \geq xc \geq 0 \\
1 \geq yc \geq 0 \\
1  \geq za \geq 0 \\
\end{cases}
\\ \downarrow \\
\begin{cases}
1 \geq yc + 1 - za \geq 0 \\
1 \geq xc + za - 1 \geq 0 \\
1 \geq yc - xc + 1 \geq 0 \\
\end{cases}
\\ \downarrow \\
\begin{cases}
0 \geq yc - za \geq -1 \\
2 \geq yc + za \geq 1 \\
0 \geq yc - xc \geq -1 \\
\end{cases}
\\ \downarrow \\
\begin{cases}
za \geq yc \\
xc + za \geq 1 \\
xc \geq yc
\end{cases}
\\ \\
_\text{最终得到的不等式, 则是我们的 x 和 y 需要满足的条件.} \\\\
_\text{只有 x 和 y 像素满足这些条件, za 和 zc 才有值}
$$

- 最终我们需要的算式是这些:
$$
\begin{cases}
zc = yc \div za \\
za = yc - xc + 1
\end{cases}
$$

- 而 ARGB 通道的值是 0 ~ 255, 所以需要进行转换一下:
$$
\begin{cases}
zc = yc \div (za \div 255) = yc \times 255 \div za \\
za = yc - xc + 255
\end{cases}
$$

- 而刚刚我们需要满足的条件, 其中只有一个是我们真正需要进行处理的, 即:
$$
xc \geq yc
$$


## 解决方案:

关于 $xc \geq yc$ 的条件, 很简单, $xc$ 与 $yc$ 的值是在 $0$ 到 $255$ 之内的, 那我们只需要将其压制到 $128$ 到 $255$ 之间, 将 $yc$ 压制到 $0$ 到 $127$ 之间, 即可解决. 然后就可以直接用我们得出的公式来运算了.


```csharp
Color CalcPixel(Color x, Color y)
{
	int
		xc = (x.R + x.G + x.B) / 3,
		yc = (y.R + y.G + z.B) / 3;   // 获取亮度
	
	xc = (xc / 255f) * 127 + 128;
	yc = (yc / 255f) * 127;           // 压制颜色

	int
		za = yc - xc + 255,
		zc = za == 0 ? 0 : yc * 255 / za;           // 运算结果颜色

	return Color.FromArgb(ya, yc, yc, yc);
}
```


我们还可以加点功能, 就是 x 与 y 的颜色占用比例, 例如, 刚刚的就是 1:1, 如果是 10 : 245, 则 x 占用 245 ~ 255, y 占用 0 ~ 245.


```csharp
// src1ColorRatio 为 x 的占用比例, 值域是0~1
Color CalcPixel(Color src1, Color src2, float src1ColorRatio = 0.5f)
{
    float src2ColorRatio = 1 - src1ColorRatio;   // 运算出 y 的占用比例

    int
        xc = (int)((src1.R + src1.G + src1.B) * src1ColorRatio / 3 + src2ColorRatio * 255 + 1),
        yc = (int)((src2.R + src2.G + src2.B) * src2ColorRatio / 3);

    int za = yc - xc + 255,
        zc = za == 0 ? 0 : (yc * 255 / za);

    return Color.FromArgb(za, zc, zc, zc);
}
```


## 完整代码:

> 需要的库: 
> 1. System.Drawing 程序集或 System.Drawing.Common 包.


LockBitmap 源码:

```csharp
using System;
using System.Drawing;
using System.Drawing.Imaging;
using System.Runtime.InteropServices;

namespace Null.PhantomTank.Library
{
    public class LockBitmap : IDisposable
    {
        Bitmap source = null;
        IntPtr Iptr = IntPtr.Zero;
        BitmapData bitmapData = null;

        public byte[] Pixels { get; set; }
        public int Depth { get; private set; }
        public int Width { get; private set; }
        public int Height { get; private set; }
        public bool IsLocked { get; private set; }

        private Func<int, Color> colorGetter;
        private Action<int, Color> colorSetter;

        public LockBitmap(Bitmap source)
        {
            this.source = source;
            LockBits();
        }

        /// <summary>
        /// Lock bitmap data
        /// </summary>
        public void LockBits()
        {
            try
            {
                // Get width and height of bitmap
                Width = source.Width;
                Height = source.Height;

                // get total locked pixels count
                //int PixelCount = Width * Height;

                // Create rectangle to lock
                System.Drawing.Rectangle rect = new System.Drawing.Rectangle(0, 0, Width, Height);

                // get source bitmap pixel format size
                Depth = System.Drawing.Bitmap.GetPixelFormatSize(source.PixelFormat);

                // Check if bpp (Bits Per Pixel) is 8, 24, or 32
                if (Depth == 8)
                {
                    colorGetter = (offset) => Color.FromArgb(Pixels[offset], Pixels[offset], Pixels[offset]);
                    colorSetter = (offset, color) =>
                    {
                        Pixels[offset] = color.B;
                    };
                }
                else if (Depth == 24)
                {
                    colorGetter = (offset) => Color.FromArgb(Pixels[offset + 2], Pixels[offset + 1], Pixels[offset]);
                    colorSetter = (offset, color) =>
                    {
                        Pixels[offset] = color.B;
                        Pixels[offset + 1] = color.G;
                        Pixels[offset + 2] = color.R;
                    };
                }
                else if (Depth == 32)
                {
                    colorGetter = (offset) => Color.FromArgb(Pixels[offset + 3], Pixels[offset + 2], Pixels[offset + 1], Pixels[offset]);
                    colorSetter = (offset, color) =>
                    {
                        Pixels[offset] = color.B;
                        Pixels[offset + 1] = color.G;
                        Pixels[offset + 2] = color.R;
                        Pixels[offset + 3] = color.A;
                    };
                }
                else
                {
                    throw new ArgumentException("Only 8, 24 and 32 bpp images are supported.");
                }

                // Lock bitmap and return bitmap data
                bitmapData = source.LockBits(rect, ImageLockMode.ReadWrite,
                                             source.PixelFormat);

                // create byte array to copy pixel values
                int step = Depth / 8;
                Pixels = new byte[bitmapData.Stride * Height];
                Iptr = bitmapData.Scan0;

                IsLocked = true;

                // Copy data from pointer to array
                Marshal.Copy(Iptr, Pixels, 0, Pixels.Length);
            }
            catch (Exception ex)
            {
                throw ex;
            }
        }

        /// <summary>
        /// Unlock bitmap data
        /// </summary>
        public void UnlockBits()
        {
            try
            {
                // Copy data from byte array to pointer
                Marshal.Copy(Pixels, 0, Iptr, Pixels.Length);

                // Unlock bitmap data
                source.UnlockBits(bitmapData);

                IsLocked = false;
            }
            catch (Exception ex)
            {
                throw ex;
            }
        }

        /// <summary>
        /// Get the color of the specified pixel
        /// </summary>
        /// <param name="x"></param>
        /// <param name="y"></param>
        /// <returns></returns>
        public Color GetPixel(int x, int y)
        {
            Color clr = Color.Empty;

            // Get color components count
            int cCount = Depth / 8;

            // Get start index of the specified pixel
            //int i = ((y * Width) + x) * cCount;
            int i = y * bitmapData.Stride + x * cCount;

            if (i > Pixels.Length - cCount)
                throw new IndexOutOfRangeException();

            // Get color by array index
            clr = colorGetter.Invoke(i);

            return clr;
        }

        /// <summary>
        /// Set the color of the specified pixel
        /// </summary>
        /// <param name="x"></param>
        /// <param name="y"></param>
        /// <param name="color"></param>
        public void SetPixel(int x, int y, Color color)
        {
            // Get color components count
            int cCount = Depth / 8;

            // Get start index of the specified pixel
            //int i = ((y * Width) + x) * cCount;
            int i = y * bitmapData.Stride + x * cCount;

            // Set color by array index and color object
            colorSetter.Invoke(i, color);
        }

        public bool IsValidCoordinate(int x, int y)
        {
            return x >= 0 && x < this.Width && y > 0 && y < this.Height;
        }

        #region IDisposable Support
        private bool disposedValue = false; // 要检测冗余调用

        protected virtual void Dispose(bool disposing)
        {
            if (!disposedValue)
            {
                if (disposing)
                {
                    // TODO: 释放托管状态(托管对象)。
                }

                // TODO: 释放未托管的资源(未托管的对象)并在以下内容中替代终结器。
                // TODO: 将大型字段设置为 null。
                UnlockBits();
                disposedValue = true;
            }
        }

        // TODO: 仅当以上 Dispose(bool disposing) 拥有用于释放未托管资源的代码时才替代终结器。
        // ~LockBitmap() {
        //   // 请勿更改此代码。将清理代码放入以上 Dispose(bool disposing) 中。
        //   Dispose(false);
        // }

        // 添加此代码以正确实现可处置模式。
        void IDisposable.Dispose()
        {
            // 请勿更改此代码。将清理代码放入以上 Dispose(bool disposing) 中。
            Dispose(true);
            // TODO: 如果在以上内容中替代了终结器，则取消注释以下行。
            // GC.SuppressFinalize(this);
        }
        #endregion
    }
}
```

幻影坦克制作源码:

```csharp
using System;
using System.Collections.Generic;
using System.Drawing;
using System.Linq;
using System.Text;
using Null.PhantomTank.Library;

namespace Null.PhantomTank
{
    public static class PhantomTank
    {
        /// 像素运算方式 变量 : 源色: Xc Yc, 输出: Za Zc
        ///   其中: c表示Color, 即颜色亮度, a代表Alpha通道, 即不透明度
        ///   备注: 值全部为0~1的比值
        ///
        /// Expr: | Xc = Za * Zc + 1 - Za
        ///       | Yc = Za * Zc
        /// 
        /// Calc: | Xc = Yc + 1 - Za
        ///       | Yc = Xc + Za - 1
        ///       | Za = Yc - Xc + 1
        /// 
        /// Need: | 1 >= Yc + 1 - Za >= 0, 0 >= Yc - Za >= -1;      Then: | Za >= Yc
        ///       | 1 >= Xc + Za - 1 >= 0, 2 >= Xc + Za >= 1;       Then: | Xc + Za >= 1
        ///       | 1 >= Yc - Xc + 1 >= 0, 0 >= Yc - Xc >= -1;      Then: | Xc >= Yc
        /// 
        /// Root: | Zc = Yc / Za
        ///       | Za = Yc - Xc + 1
        ///       
        ///       Basic Root: | Zc = Yc / (Za / 255) = Yc * 255 / Za
        ///                   | Za = Yc - Xc + 255

        private static Color CalcPixel(Color src1, Color src2, float src1ColorRatio = 0.5f)
        {
            float src2ColorRatio = 1 - src1ColorRatio;

            int
                xc = (int)((src1.R + src1.G + src1.B) * src1ColorRatio / 3 + src2ColorRatio * 255),
                yc = (int)((src2.R + src2.G + src2.B) * src2ColorRatio / 3);

            int
                za = yc - xc + 255,
                zc = za == 0 ? 0 : (yc * 255 / za);

            return Color.FromArgb(za, zc, zc, zc);
        }
        private static float ConvertRatio(float ratio)
        {
            return ratio / (ratio + 1);
        }
        public const float DefaultRatio = 1;
        public static Bitmap ResizeBitmap(Bitmap src, Color bgColor, ResizeMode resize, int newWidth, int newHeight)
        {
            Size srcSize = src.Size;

            int srcWidth = srcSize.Width,
                srcHeight = srcSize.Height;

            Bitmap result = new Bitmap(newWidth, newHeight, src.PixelFormat);
            Graphics rstG = Graphics.FromImage(result);

            rstG.Clear(bgColor);

            Size destSize;
            Rectangle srcRect, destRect;

            switch(resize)
            {
                case ResizeMode.NoResize:
                    destRect = new Rectangle((newWidth - srcWidth) / 2, (newHeight - srcHeight) / 2, srcWidth, srcHeight);
                    rstG.DrawImageUnscaled(src, destRect);
                    break;
                case ResizeMode.Stretch:
                    srcRect = new Rectangle(0, 0, srcWidth, srcHeight);
                    destRect = new Rectangle(0, 0, newWidth, newHeight);
                    rstG.DrawImage(src, destRect, srcRect, GraphicsUnit.Pixel);
                    break;
                case ResizeMode.Uniform:
                    srcRect = new Rectangle(0, 0, srcWidth, srcHeight);
                    destSize = new Size(newWidth, srcHeight * newWidth / srcWidth);
                    if (destSize.Height > newHeight)
                        destSize = new Size(srcWidth * newHeight / srcHeight, newHeight);
                    destRect = new Rectangle(new Point((newWidth - destSize.Width) / 2, (newHeight - destSize.Height) / 2), destSize);
                    rstG.DrawImage(src, destRect, srcRect, GraphicsUnit.Pixel);
                    break;
                case ResizeMode.UniformToFill:
                    srcRect = new Rectangle(0, 0, srcWidth, srcHeight);
                    destSize = new Size(newWidth, srcHeight * newWidth / srcWidth);
                    if (destSize.Height < newHeight)
                        destSize = new Size(srcWidth * newHeight / srcHeight, newHeight);
                    destRect = new Rectangle(new Point((newWidth - destSize.Width) / 2, (newHeight - destSize.Height) / 2), destSize);
                    rstG.DrawImage(src, destRect, srcRect, GraphicsUnit.Pixel);
                    break;
            }

            return result;
        }

        /// <summary>
        /// 最基本的合成方法, 请保证图片尺寸是一致的
        /// </summary>
        /// <param name="src1">在白底下可以看到的图片</param>
        /// <param name="src2">在黑底下可以看到的图片</param>
        /// <returns>合并后的黑白图像</returns>
        public static Bitmap BasicCombineBitmap(Bitmap src1, Bitmap src2, float colorRatio)
        {
            if (src1 == null || src2 == null)
                throw new ArgumentNullException();
            if (src1.Size != src2.Size)
                throw new ArgumentOutOfRangeException();

            Size srcSize = src1.Size;
            int width = srcSize.Width, height = srcSize.Height;

            Bitmap result = new Bitmap(width, height, src1.PixelFormat);
            result.SetResolution(src1.HorizontalResolution, src1.VerticalResolution);

            LockBitmap lbmp1 = new LockBitmap(src1);
            LockBitmap lbmp2 = new LockBitmap(src2);
            LockBitmap lrst = new LockBitmap(result);

            float whiteRatio = ConvertRatio(colorRatio);

            for (int i = 0; i < height; i++)
            {
                for (int j = 0; j < width; j++)
                {
                    Color
                        srcPixel1 = lbmp1.GetPixel(j, i),
                        srcPixel2 = lbmp2.GetPixel(j, i),
                        outPixel = CalcPixel(srcPixel1, srcPixel2, whiteRatio);

                    lrst.SetPixel(j, i, outPixel);
                }
            }

            lbmp1.UnlockBits();
            lbmp2.UnlockBits();
            lrst.UnlockBits();

            return result;
        }

        /// <summary>
        /// 转换图片
        /// </summary>
        /// <param name="src">源图</param>
        /// <param name="tankType">坦克类型</param>
        /// <returns>转换结果</returns>
        public static Bitmap ConvertBitmap(Bitmap src, TankType tankType)
        {
            Size srcSize = src.Size;

            int
                srcWidth = srcSize.Width,
                srcHeight = srcSize.Height;

            Bitmap result = new Bitmap(srcWidth, srcHeight, src.PixelFormat);
            result.SetResolution(src.HorizontalResolution, src.VerticalResolution);

            LockBitmap lsrc = new LockBitmap(src);
            LockBitmap lrst = new LockBitmap(result);

            Func<int, Color> pixelCalcFunc;
            switch (tankType)
            {
                case TankType.AppearOnBlack:
                    pixelCalcFunc = (srcPixel) => Color.FromArgb(srcPixel, 255, 255, 255);
                    break;
                case TankType.AppearOnWhite:
                    pixelCalcFunc = (srcPixel) => Color.FromArgb(255 - srcPixel, 0, 0, 0);
                    break;
                default:
                    throw new InvalidOperationException("Not supported.");
            }

            for (int i = 0; i < srcHeight; i++)
            {
                for (int j = 0; j < srcWidth; j++)
                {
                    Color pixel = lsrc.GetPixel(j, i);
                    int light = (pixel.R + pixel.G + pixel.B) / 3;
                    lrst.SetPixel(j, i, pixelCalcFunc.Invoke(light));
                }
            }

            lsrc.UnlockBits();
            lrst.UnlockBits();

            return result;
        }
        public static Bitmap ConvertImage(Image src, TankType tankType)
        {
            Bitmap newSrc = new Bitmap(src);
            Bitmap result = ConvertBitmap(newSrc, tankType);

            newSrc.Dispose();
            return result;
        }

        public static Bitmap CombineBitmap(Bitmap src1, Bitmap src2, Color bgColor1, Color bgColor2, ResizeMode resize, float colorRatio)
        {
            Size
                src1Size = src1.Size,
                src2Size = src2.Size;

            int src1Width = src1Size.Width,
                src1Height = src1Size.Height,
                src2Width = src2Size.Width,
                src2Height = src2Size.Height;

            int
                maxWidth = src1Width > src2Width ? src1Width : src2Width,
                maxHeight = src1Height > src2Height ? src1Height : src2Height;

            Bitmap
                newSrc1 = new Bitmap(maxWidth, maxHeight, src1.PixelFormat),
                newSrc2 = new Bitmap(maxWidth, maxHeight, src2.PixelFormat);

            Graphics
                srcG1 = Graphics.FromImage(newSrc1),
                srcG2 = Graphics.FromImage(newSrc2);

            srcG1.Clear(bgColor1);
            srcG2.Clear(bgColor2);

            // 这里进行的是对图片的重新调整尺寸操作
            switch(resize)
            {
                case ResizeMode.NoResize:
                    srcG1.DrawImageUnscaled(src1, (maxWidth - src1Width) / 2, (maxHeight - src1Height) / 2);
                    srcG2.DrawImageUnscaled(src2, (maxWidth - src2Width) / 2, (maxHeight - src2Height) / 2);
                    break;
                case ResizeMode.Stretch:
                    srcG1.DrawImage(src1, new Rectangle(0, 0, maxWidth, maxHeight), new Rectangle(0, 0, src1Width, src1Height), GraphicsUnit.Pixel);
                    srcG2.DrawImage(src2, new Rectangle(0, 0, maxWidth, maxHeight), new Rectangle(0, 0, src2Width, src2Height), GraphicsUnit.Pixel);
                    break;
                case ResizeMode.Uniform:
                    Size
                        scaleSize1 = new Size(maxWidth, (int)(src1Height * ((float)maxWidth / src1Width))),
                        scaleSize2 = new Size(maxWidth, (int)(src2Height * ((float)maxWidth / src2Width)));
                    if (scaleSize1.Height > maxHeight)
                        scaleSize1 = new Size((int)(src1Width * ((float)maxHeight / src1Height)), maxHeight);
                    if (scaleSize2.Height > maxHeight)
                        scaleSize2 = new Size((int)(src2Width * ((float)maxHeight / src2Height)), maxHeight);
                    srcG1.DrawImage(src1, new Rectangle(new Point((maxWidth - scaleSize1.Width) / 2, (maxHeight - scaleSize1.Height) / 2), scaleSize1), new Rectangle(0, 0, src1Width, src1Height), GraphicsUnit.Pixel);
                    srcG2.DrawImage(src2, new Rectangle(new Point((maxWidth - scaleSize2.Width) / 2, (maxHeight - scaleSize2.Height) / 2), scaleSize2), new Rectangle(0, 0, src2Width, src2Height), GraphicsUnit.Pixel);
                    break;
                case ResizeMode.UniformToFill:
                    Size
                        scaleFillSize1 = new Size(maxWidth, (int)(src1Height * ((float)maxWidth / src1Width))),
                        scaleFillSize2 = new Size(maxWidth, (int)(src2Height * ((float)maxWidth / src2Width)));
                    if (scaleFillSize1.Height < maxHeight)
                        scaleFillSize1 = new Size((int)(src1Width * ((float)maxHeight / src1Height)), maxHeight);
                    if (scaleFillSize2.Height < maxHeight)
                        scaleFillSize2 = new Size((int)(src2Width * ((float)maxHeight / src2Height)), maxHeight);
                    srcG1.DrawImage(src1, new Rectangle(new Point((maxWidth - scaleFillSize1.Width) / 2, (maxHeight - scaleFillSize1.Height) / 2), scaleFillSize1), new Rectangle(0, 0, src1Width, src1Height), GraphicsUnit.Pixel);
                    srcG2.DrawImage(src2, new Rectangle(new Point((maxWidth - scaleFillSize2.Width) / 2, (maxHeight - scaleFillSize2.Height) / 2), scaleFillSize2), new Rectangle(0, 0, src2Width, src2Height), GraphicsUnit.Pixel);
                    break;
            }

            srcG1.Dispose();
            srcG2.Dispose();

            Bitmap result = BasicCombineBitmap(newSrc1, newSrc2, colorRatio);    // 调用最终合成方法
            newSrc1.Dispose();
            newSrc2.Dispose();

            return result;
        }
        public static Bitmap CombineBitmap(Bitmap src1, Bitmap src2, Color bgColor1, Color bgColor2, ResizeMode resize)
        {
            return CombineBitmap(src1, src2, Color.White, Color.Black, resize, DefaultRatio);
        }
        public static Bitmap CombineBitmap(Bitmap src1, Bitmap src2, Color bgColor1, Color bgColor2, float colorRatio)
        {
            return CombineBitmap(src1, src2, Color.White, Color.Black, ResizeMode.NoResize, DefaultRatio);
        }
        public static Bitmap CombineBitmap(Bitmap src1, Bitmap src2, ResizeMode resize, float colorRatio)
        {
            return CombineBitmap(src1, src2, Color.White, Color.Black, resize, colorRatio);
        }
        public static Bitmap CombineBitmap(Bitmap src1, Bitmap src2, ResizeMode resize)
        {
            return CombineBitmap(src1, src2, Color.White, Color.Black, resize, 0);
        }
        public static Bitmap CombineBitmap(Bitmap src1, Bitmap src2, float colorRatio)
        {
            return CombineBitmap(src1, src2, Color.White, Color.Black, ResizeMode.NoResize, colorRatio);
        }
        public static Bitmap CombineBitmap(Bitmap src1, Bitmap src2)
        {
            return CombineBitmap(src1, src2, Color.White, Color.Black, ResizeMode.NoResize, DefaultRatio);
        }

        public static Bitmap CombineImage(Image src1, Image src2, Color bgColor1, Color bgColor2, ResizeMode resize, float colorRatio)
        {
            Bitmap
                newSrc1 = new Bitmap(src1),
                newSrc2 = new Bitmap(src2);

            Bitmap result = CombineBitmap(newSrc1, newSrc2, bgColor1, bgColor2, resize, colorRatio);
            newSrc1.Dispose();
            newSrc2.Dispose();

            return result;
        }
        public static Bitmap CombineImage(Image src1, Image src2, Color bgColor1, Color bgColor2, ResizeMode resize)
        {
            return CombineImage(src1, src2, Color.White, Color.Black, resize, DefaultRatio);
        }
        public static Bitmap CombineImage(Image src1, Image src2, Color bgColor1, Color bgColor2, float colorRatio)
        {
            return CombineImage(src1, src2, Color.White, Color.Black, ResizeMode.NoResize, colorRatio);
        }
        public static Bitmap CombineImage(Image src1, Image src2, ResizeMode resize, float colorRatio)
        {
            return CombineImage(src1, src2, Color.White, Color.Black, resize, colorRatio);
        }
        public static Bitmap CombineImage(Image src1, Image src2, ResizeMode resize)
        {
            return CombineImage(src1, src2, Color.White, Color.Black, resize, DefaultRatio);
        }
        public static Bitmap CombineImage(Image src1, Image src2, float colorRatio)
        {
            return CombineImage(src1, src2, Color.White, Color.Black, ResizeMode.NoResize, colorRatio);
        }
        public static Bitmap CombineImage(Image src1, Image src2)
        {
            return CombineImage(src1, src2, Color.White, Color.Black, ResizeMode.NoResize, DefaultRatio);
        }
    }
    public enum ResizeMode
    {
        NoResize,
        Stretch,
        Uniform,
        UniformToFill,
    }
    public enum TankType
    {
        AppearOnBlack,
        AppearOnWhite,
    }
}
```


## $\downarrow$ 点个赞再走呗! $\downarrow$
