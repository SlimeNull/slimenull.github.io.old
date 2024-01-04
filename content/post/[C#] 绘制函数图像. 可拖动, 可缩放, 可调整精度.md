---
title: '[C#] 绘制函数图像. 可拖动, 可缩放, 可调整精度'
slug: '20210219220053'
date: 2021-02-19T22:00:53+08:00
tags:
  - csharp
  - dotnet
  - csharp
categories:
  - dotnet
  - 桌面程序
  - 类库
description: '欸嘿, 这就是程序图了, 通过鼠标拖拽可以移动, 鼠标滚轮可以缩放, 右下角还可以选择要绘制的函数. 项目仓库链接在文章末尾基本原理:Graphics 绘图, 不用我说了吧? 如果你不是很懂, 留言, 我会专门写一篇文章来介绍 Graphics.带入求值, 没啥难的. 线是一个个点连起来的, 也就是:然后, 标尺, 也是一个个线呗, 那个数字的话, 就是这个:填充小三角的话, 就是这个:关于优化:首先是计算问题, 保证仅仅计算需要显示的区域, 区域外的坐标不予以计算, 以节省资源.然.'
---

![](images/b50d551223eb9fdb405aa2f1058e3eee.gif)
欸嘿, 这就是程序图了, 通过鼠标拖拽可以移动, 鼠标滚轮可以缩放, 右下角还可以选择要绘制的函数. 项目仓库链接在文章末尾


## 基本原理:

Graphics 绘图, 不用我说了吧? 如果你不是很懂, 留言, 我会专门写一篇文章来介绍 Graphics.


带入求值, 没啥难的. 线是一个个点连起来的, 也就是:
![](images/20210219052221569.png)
然后, 标尺, 也是一个个线呗, 那个数字的话, 就是这个:
![](images/20210219052448721.png)
填充小三角的话, 就是这个:
![](images/20210219052539913.png)

## 关于优化:

首先是计算问题, 保证仅仅计算需要显示的区域, 区域外的坐标不予以计算, 以节省资源.


然后是闪屏问题, 使用BufferedGraphics, 既能解决闪屏问题, 又不会像Bitmap缓冲那样闪屏.


> 关于绘图闪屏问题, 网上有很多解决方案, 最核心的, 无非是双缓冲, 也就是先将图绘制到缓冲区, 再将缓冲区的内容绘制到屏幕上.
> &nbsp;
> 实现双缓冲有两种方式, 一就是通过创建一个Bitmap, 将图画到Bitmap上, 然后画完之后, 再将Bitmap画到屏幕上(Graphics.DrawImage()), 缺点是会造成撕裂问题.
> 第二种方式是 BufferedGraphics, 这是一个比Bitmap更好用的东西, 不会造成撕裂.

## 封装一下:

```csharp
using System;
using System.Collections.Generic;
using System.Drawing;
using System.Linq;

namespace Null.FuncDraw
{
    public static class FuncDraw
    {
        /// <summary>
        /// 根据数字坐标获取像素位置
        /// </summary>
        /// <param name="xCoord">x坐标</param>
        /// <param name="yCoord">y坐标</param>
        /// <param name="xOffset">原点位置</param>
        /// <param name="yOffset">原点位置</param>
        /// <param name="scale">缩放</param>
        /// <returns>像素位置</returns>
        public static Point GetPointFromCoords(double xCoord, double yCoord, int xOffset, int yOffset, double scale)
        {
            return new Point((int)(xCoord * scale + xOffset), (int)(-yCoord * scale + yOffset));
        }
        /// <summary>
        /// 
        /// </summary>
        /// <param name="coords">数字坐标</param>
        /// <param name="offset">原点位置</param>
        /// <param name="scale">缩放</param>
        /// <returns>像素位置</returns>
        public static Point GetPointFromCoords(PointF coords, Point offset, double scale)
        {
            return new Point((int)(coords.X * scale + offset.X), (int)(-coords.Y * scale + offset.Y));
        }
        /// <summary>
        /// 根据像素位置获取数字坐标
        /// </summary>
        /// <param name="x">水平位置</param>
        /// <param name="y">竖直位置</param>
        /// <param name="xOffset">原点位置</param>
        /// <param name="yOffset">原点位置</param>
        /// <param name="scale">缩放</param>
        /// <param name="xCoord">输出: 数字X坐标</param>
        /// <param name="yCoord">输出: 数字Y坐标</param>
        public static void GetCoordsFromPoint(int x, int y, int xOffset, int yOffset, double scale, out double xCoord, out double yCoord)
        {
            xCoord = (x - xOffset) / scale;
            yCoord = -((y - yOffset) / scale);
            return;
        }
        /// <summary>
        /// 根据像素长度返回数字
        /// </summary>
        /// <param name="length">像素长度</param>
        /// <param name="scale">缩放</param>
        /// <returns>数字</returns>
        public static double GetNumberFromPixel(int length, double scale)
        {
            return length / scale;
        }
        /// <summary>
        /// 根据数字来获取它距离原点的像素长度
        /// </summary>
        /// <param name="number">数字</param>
        /// <param name="scale">缩放</param>
        /// <returns>像素长度</returns>
        public static double GetPixelFromNumber(int number, double scale)
        {
            return number * scale;
        }
        /// <summary>
        /// 画函数图像
        /// </summary>
        /// <param name="func">要画的函数</param>
        /// <param name="inputs">X的取值</param>
        /// <param name="graphics">绘图Graphics</param>
        /// <param name="pen">画线所用的Pen</param>
        /// <param name="drawArea">画函数的区域</param>
        /// <param name="xOffset">原点的位置</param>
        /// <param name="yOffset">原点的位置</param>
        /// <param name="scale">缩放参数</param>
        public static void DrawFunc(Func<double, double> func, IEnumerable<double> inputs, Graphics graphics, Pen pen, Rectangle drawArea, int xOffset, int yOffset, double scale)
        {
            double[] nums = inputs.ToArray();
            Point[] coords = new Point[nums.Length];

            int drawAreaLeft = drawArea.X,
                drawAreaRight = drawAreaLeft + drawArea.Width,
                drawAreaTop = drawArea.Y,
                drawAreaBottom = drawAreaTop + drawArea.Height;

            double y;
            for (int i = 0, len = nums.Length; i < len; i++)          // 最近刚知道要少用属性, 毕竟属性的实质是函数, 函数调用需要分配栈空间, 而且似乎还涉及到装箱拆箱, 所以这样效率高一些
            {
                y = func.Invoke(nums[i]);
                coords[i] = GetPointFromCoords(nums[i], y, xOffset, yOffset, scale);
            }

            bool point1xIn1,
                point1xIn2,
                point1yIn1,
                point1yIn2,
                point2xIn1,
                point2xIn2,
                point2yIn1,
                point2yIn2;

            for (int i = 1, len = nums.Length; i < len; i++)
            {
                Point point1 = coords[i - 1];
                Point point2 = coords[i];

                point1xIn1 = point1.X >= drawAreaLeft;
                point1xIn2 = point1.X <= drawAreaRight;
                point1yIn1 = point1.Y >= drawAreaTop;
                point1yIn2 = point1.Y <= drawAreaBottom;
                point2xIn1 = point2.X >= drawAreaLeft;
                point2xIn2 = point2.X <= drawAreaRight;
                point2yIn1 = point2.Y >= drawAreaTop;
                point2yIn2 = point2.Y <= drawAreaBottom;

                if ((!(point1xIn1 && point2xIn1)) || (!(point1xIn2 && point2xIn2)) || (!(point1yIn1 && point2yIn1)) || (!(point1yIn2 && point2yIn2)))
                    continue;

                if (!point1xIn1)
                    point1 = new Point(drawAreaLeft, (int)(point1.Y + (point2.Y - point1.Y) * ((double)drawAreaLeft - point1.X) / (point2.X - point1.X)));      // 这里是有一些奇妙优化的, 不要删去. 否则将导致某些函数无法绘制.
                else if (!point2xIn1)
                    point2 = new Point(drawAreaLeft, (int)(point2.Y + (point1.Y - point2.Y) * ((double)drawAreaLeft - point2.X) / (point1.X - point2.X)));
                if (!point1xIn2)
                    point1 = new Point(drawAreaRight, (int)(point2.Y + (point1.Y - point2.Y) * ((double)drawAreaRight - point2.X) / (point1.X - point2.X)));
                else if (!point2xIn2)
                    point2 = new Point(drawAreaRight, (int)(point1.Y + (point2.Y - point1.Y) * ((double)drawAreaRight - point1.X) / (point2.X - point1.X)));
                if (!point1yIn1) 
                    point1 = new Point((int)(point1.X + (point2.X - point1.X) * ((double)drawAreaTop - point1.Y) / (point2.Y - point1.Y)), drawAreaTop);
                else if (!point2yIn1)
                    point2 = new Point((int)(point2.X + (point1.X - point2.X) * ((double)drawAreaTop - point2.Y) / (point1.Y - point2.Y)), drawAreaTop);
                if (!point1yIn2)
                    point1 = new Point((int)(point2.X + (point1.X - point2.X) * ((double)drawAreaBottom - point2.Y) / (point1.Y - point2.Y)), drawAreaBottom);
                else if (!point2yIn2)
                    point2 = new Point((int)(point1.X + (point2.X - point1.X) * ((double)drawAreaBottom - point1.Y) / (point2.Y - point1.Y)), drawAreaBottom);

                graphics.DrawLine(pen, point1, point2);
            }
        }
        /// <summary>
        /// 画一个坐标轴
        /// </summary>
        /// <param name="xNumbers">x轴要画的数</param>
        /// <param name="yNumbers">y轴要画的数</param>
        /// <param name="graphics">绘图Graphics</param>
        /// <param name="brush">填充坐标轴三角所使用的Brush</param>
        /// <param name="pen">画坐标轴所使用的Pen</param>
        /// <param name="font">画坐标数字所使用的Font</param>
        /// <param name="drawArea">画坐标轴的区域</param>
        /// <param name="xOffset">原点的位置</param>
        /// <param name="yOffset">远点的位置</param>
        /// <param name="barLength">坐标轴上小竖线的长度</param>
        /// <param name="scale">缩放参数</param>
        /// <param name="text">是否绘出数字</param>
        public static void DrawShaft(IEnumerable<double> xNumbers, IEnumerable<double> yNumbers, Graphics graphics, Brush brush, Pen pen, Font font, Rectangle drawArea, int xOffset, int yOffset, int barLength, double scale, bool text)
        {
            Action<Point, double> drawText;

            if (text)
                drawText = (point, num) => graphics.DrawString(num.ToString("F2"), font, brush, point);      // 使用委托, 那么接下来就不需要进行多余的对text的判断, 而直接Invoke就彳亍
            else
                drawText = (point, num) => { };

            Point
                yTop = new Point(xOffset, drawArea.Y),
                yBottom = new Point(xOffset, drawArea.Y + drawArea.Height),       // 确认轴的位置
                xLeft = new Point(drawArea.X, yOffset),
                xRight = new Point(drawArea.X + drawArea.Width, yOffset);

            graphics.DrawLine(pen, yTop, yBottom);            // 画轴
            graphics.DrawLine(pen, xLeft, xRight);

            int triangleHeight = (int)(Math.Tan(Math.PI / 3) * barLength);        // 坐标轴末端小三角的高度
            Point[]
                triangle1 = new Point[] { yTop, new Point(yTop.X - barLength, yTop.Y + triangleHeight), new Point(yTop.X + barLength, yTop.Y + triangleHeight) },
                triangle2 = new Point[] { xRight, new Point(xRight.X - triangleHeight, xRight.Y + barLength), new Point(xRight.X - triangleHeight, xRight.Y - barLength) };
            graphics.FillPolygon(brush, triangle1);           // 画三角
            graphics.FillPolygon(brush, triangle2);

            foreach (double x in xNumbers)
            {
                Point numBase = GetPointFromCoords(x, 0, xOffset, yOffset, scale);
                Point numEnd = new Point(numBase.X, numBase.Y - barLength);
                graphics.DrawLine(pen, numBase, numEnd);      // 画x轴数
                drawText(numBase, x);
            }

            foreach (double y in yNumbers)
            {
                Point numBase = GetPointFromCoords(0, y, xOffset, yOffset, scale);
                Point numEnd = new Point(numBase.X + barLength, numBase.Y);
                graphics.DrawLine(pen, numBase, numEnd);      // 画y轴数
                drawText(numBase, y);
            }
        }
    }
}
```

#### 注意事项:

~~Graphics 在绘制某些超级远的东西时, 例如这个坐标时 (2147483647,2147483647), 总之就是非常大, 那可能会不能绘制, 报 Overflow 异常~~ **(已解决)**

#### 还有一些要用的内容:

CSDN 文章: [C# 中的 Range 函数](/p/20210219070741/)
CSDN 文章: [C# IEnumerable 连接, 将迭代器串起来](/p/20210219152833/)

#### 更新记录:

最新版本已经支持自定义函数表达式


## 项目:

- **项目仓库:** [https://github.com/SlimeNull/Null.FuncDraw](https://github.com/SlimeNull/Null.FuncDraw)
