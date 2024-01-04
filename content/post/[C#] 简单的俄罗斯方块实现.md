---
title: '[C#] 简单的俄罗斯方块实现'
slug: '20230809093633'
date: 2023-08-09T09:36:33+08:00
tags:
  - csharp
  - 开发语言
  - 玩游戏
categories:
  - dotnet
  - 控制台
  - 灌水
description: '简单控制台俄罗斯方块实现'
---

一个控制台俄罗斯方块游戏的简单实现. 已在 [github.com/SlimeNull/Tetris](https://github.com/SlimeNull/Tetris) 开源.

![在这里插入图片描述](images/dd0c115c985046c6a9267bab3288cc8e.png)


<br/>


## 思路


很简单, 一个二维数组存储当前游戏的方块地图, 用 `bool` 即可, `true` 表示当前块被填充, `false` 表示没有.


然后, 抽一个 "形状" 类, 形状表示当前玩家正在操作的一个形状, 例如方块, 直线, T 形什么的. 一个形状又有不同的样式, 也就是玩家可以切换的样式. 每一个样式都是原来样式旋转之后的结果. 为了方便, 可以直接使用硬编码的方式存储所有样式中方块的相对坐标.


一个形状有一个自己的坐标, 并且它包含很多方块. 在绘制的时候, 获取它每一个方块的坐标, 转换为地图内的绝对坐标, 然后使用 `StringBuilder` 拼接字符串, 即可.


<br/>


## 资料

俄罗斯方块中总共有这七种方块


<div align="center">


![在这里插入图片描述](images/7352d9c5ae3f4f9f8f1bea49a719dae4.png)


</div>
<br/>


## 类型定义


一个简单的二维坐标

```csharp
/// <summary>
/// 表示一个坐标
/// </summary>
/// <param name="X"></param>
/// <param name="Y"></param>
record struct Coordinate(int X, int Y)
{
    /// <summary>
    /// 根据基坐标和相对坐标, 获取一个绝对坐标
    /// </summary>
    /// <param name="baseCoord"></param>
    /// <param name="relativeCoord"></param>
    /// <returns></returns>
    public static Coordinate GetAbstract(Coordinate baseCoord, Coordinate relativeCoord)
    {
        return new Coordinate(baseCoord.X + relativeCoord.X, baseCoord.Y + relativeCoord.Y);
    }
}
```


形状的一个样式, 单纯使用坐标数组存储即可.

```csharp
record struct ShapeStyle(Coordinate[] Coordinates);
```


形状


```csharp
/// <summary>
/// 形状基类
/// </summary>
abstract class Shape
{
    /// <summary>
    /// 名称
    /// </summary>
    public abstract string Name { get; }

    /// <summary>
    /// 形状的位置
    /// </summary>
    public Coordinate Position { get; set; }

    /// <summary>
    /// 形状所有的样式
    /// </summary>
    protected abstract ShapeStyle[] ShapeStyles { get; }

    /// <summary>
    /// 当前使用的样式索引
    /// </summary>
    private int _currentStyleIndex = 0;

    /// <summary>
    /// 从坐标构建一个新形状
    /// </summary>
    /// <param name="position"></param>
    public Shape(Coordinate position)
    {
        Position = position;
    }

    /// <summary>
    /// 获取当前形状的当前所有方块 (相对坐标)
    /// </summary>
    /// <returns></returns>
    public IEnumerable<Coordinate> GetBlocks()
    {
        return ShapeStyles[_currentStyleIndex].Coordinates;
    }

    /// <summary>
    /// 获取当前形状下一个样式的所有方块 (相对坐标)
    /// </summary>
    /// <returns></returns>
    public IEnumerable<Coordinate> GetNextStyleBlocks()
    {
        return ShapeStyles[(_currentStyleIndex + 1) % ShapeStyles.Length].Coordinates;
    }

    /// <summary>
    /// 改变样式
    /// </summary>
    public void ChangeStyle()
    {
        _currentStyleIndex = (_currentStyleIndex + 1) % ShapeStyles.Length;
    }
}
```


一个 T 形状的实现


```csharp
class ShapeT : Shape
{
    public ShapeT(Coordinate position) : base(position)
    {
    }

    public override string Name => "T";

    protected override ShapeStyle[] ShapeStyles { get; } = new ShapeStyle[]
    {
        new ShapeStyle(
            new Coordinate[]
            {
                new Coordinate(-1, 0),
                new Coordinate(0, 0),
                new Coordinate(1, 0),
                new Coordinate(0, 1),
            }),
        new ShapeStyle(
            new Coordinate[]
            {
                new Coordinate(-1, 0),
                new Coordinate(0, -1),
                new Coordinate(0, 0),
                new Coordinate(0, 1),
            }),
        new ShapeStyle(
            new Coordinate[]
            {
                new Coordinate(-1, 0),
                new Coordinate(0, 0),
                new Coordinate(1, 0),
                new Coordinate(0, -1),
            }),
        new ShapeStyle(
            new Coordinate[]
            {
                new Coordinate(1, 0),
                new Coordinate(0, -1),
                new Coordinate(0, 0),
                new Coordinate(0, 1),
            }),
    };
}
```


<br/>


## 主逻辑


上面的定义已经写好了, 接下来就是写游戏主逻辑.


主逻辑包含每一回合自动向下移动形状, 如果无法继续向下移动, 则把当前的形状存储到地图中. 并进行一次扫描, 将所有的整行全部消除.


抽一个 `TetrisGame` 的类用来表示俄罗斯方块游戏, 下面是这个类的基本定义.


```csharp
class TetrisGame
{
    /// <summary>
    /// x, y
    /// </summary>
    private readonly bool[,] map;

    private readonly Random random = new Random();


    public TetrisGame(int width, int height)
    {
        map = new bool[width, height];

        Width = width;
        Height = height;
    }

    public Shape? CurrentShape { get; set; }

    public int Width { get; }
    public int Height { get; }
}
```


判断当前形状是否可以进行移动的方法

```csharp
/// <summary>
/// 判断是否可以移动 (移动后是否会与已有方块重合, 或者超出边界)
/// </summary>
/// <param name="xOffset"></param>
/// <param name="yOffset"></param>
/// <returns></returns>
private bool CanMove(int xOffset, int yOffset)
{
    // 如果当前没形状, 返回 false
    if (CurrentShape == null)
        return false;

    foreach (var block in CurrentShape.GetBlocks())
    {
        Coordinate coord = Coordinate.GetAbstract(CurrentShape.Position, block);

        coord.X += xOffset;
        coord.Y += yOffset;

        // 如果移动后方块坐标超出界限, 不能移动
        if (coord.X < 0 || coord.X >= Width ||
            coord.Y < 0 || coord.Y >= Height)
            return false;

        // 如果移动后方块会与地图现有方块重合, 则不能移动
        if (map[coord.X, coord.Y])
            return false;
    }

    return true;
}
```


判断当前形状是否能够切换到下一个样式的方法


```csharp
/// <summary>
/// 判断是否可以改变形状 (改变形状后是否会和已有方块重合, 或者超出边界)
/// </summary>
/// <returns></returns>
private bool CanChangeShape()
{
    // 如果当前没形状, 当然不能切换样式
    if (CurrentShape == null)
        return false;

    // 获取下一个样式的所有方块
    foreach (var block in CurrentShape.GetNextStyleBlocks())
    {
        Coordinate coord = Coordinate.GetAbstract(CurrentShape.Position, block);

        // 如果超出界限, 不能切换
        if (coord.X < 0 || coord.X >= Width ||
            coord.Y < 0 || coord.Y >= Height)
            return false;

        // 如果与现有方块重合, 不能切换
        if (map[coord.X, coord.Y])
            return false;
    }

    return true;
}
```


把当前形状存储到地图中

```csharp
/// <summary>
/// 将当前形状存储到地图中
/// </summary>
private void StorageShapeToMap()
{
    // 没形状, 存寂寞
    if (CurrentShape == null)
        return;

    // 所有方块遍历一下
    foreach (var block in CurrentShape.GetBlocks())
    {
        // 转为绝对坐标
        Coordinate coord = Coordinate.GetAbstract(CurrentShape.Position, block);

        // 超出界限则跳过
        if (coord.X < 0 || coord.X >= Width ||
            coord.Y < 0 || coord.Y >= Height)
            continue;

        // 存地图里
        map[coord.X, coord.Y] = true;
    }

    // 当前形状设为 null
    CurrentShape = null;
}
```


生成一个新形状


```csharp
/// <summary>
/// 生成一个新形状
/// </summary>
/// <exception cref="InvalidOperationException"></exception>
private void GenerateShape()
{
    int shapeCount = 7;
    int randint = random.Next(shapeCount);

    Coordinate initCoord = new Coordinate(Width / 2, 0);

    Shape newShape = randint switch
    {
        0 => new ShapeI(initCoord),
        1 => new ShapeJ(initCoord),
        2 => new ShapeL(initCoord),
        3 => new ShapeO(initCoord),
        4 => new ShapeS(initCoord),
        5 => new ShapeT(initCoord),
        6 => new ShapeZ(initCoord),

        _ => throw new InvalidOperationException()
    };

    CurrentShape = newShape;
}
```


扫描地图, 消除所有整行


```csharp
/// <summary>
/// 扫描, 消除掉可消除的行
/// </summary>
private void Scan()
{
    for (int y = 0;  y < Height; y++)
    {
        // 设置当前行是整行
        bool ok = true;

        // 循环当前行的所有方块, 如果方块为 false, ok 就会被设为 false
        for (int x = 0; x < Width; x++)
            ok &= map[x, y];

        // 如果当前行确实是整行
        if (ok)
        {
            // 所有行全部往下移动
            for (int _y = y; _y > 0; _y--)
                for (int x = 0; x < Width; x++)
                    map[x, _y] = map[x, _y - 1];
            
            // 最顶行全设为空
            for (int x = 0; x < Width; x++)
                map[x, 0] = false;
        }
    }
}
```


封装一些用户操作使用的方法


```csharp
/// <summary>
/// 根据指定偏移, 进行移动
/// </summary>
/// <param name="xOffset"></param>
/// <param name="yOffset"></param>
public void Move(int xOffset, int yOffse
{
    lock (this)
    {
        if (CurrentShape == null)
            return;

        if (CanMove(xOffset, yOffset))
        {
            var newCoord = CurrentShape.
            newCoord.X += xOffset;
            newCoord.Y += yOffset;

            CurrentShape.Position = newC
        }
    }
}

/// <summary>
/// 向左移动
/// </summary>
public void MoveLeft()
{
    Move(-1, 0);
}

/// <summary>
/// 向右移动
/// </summary>
public void MoveRight()
{
    Move(1, 0);
}

/// <summary>
/// 向下移动
/// </summary>
public void MoveDown()
{
    Move(0, 1);
}

/// <summary>
/// 改变形状样式
/// </summary>
public void ChangeShapeStyle()
{
    lock (this)
    {
        if (CurrentShape == null)
            return;

        if (CanChangeShape())
            CurrentShape.ChangeStyle();
    }
}

/// <summary>
/// 降落到底部
/// </summary>
public void Fall()
{
    lock (this)
    {
        while (CanMove(0, 1))
        {
            Move(0, 1);
        }
    }
}
```


游戏每一轮的主逻辑


```csharp
/// <summary>
/// 下一个回合
/// </summary>
public void NextTurn()
{
    lock (this)
    {
        // 如果当前没有存在的形状, 则生成一个新的, 并返回
        if (CurrentShape == null)
        {
            GenerateShape();
            return;
        }

        // 如果可以向下移动
        if (CanMove(0, 1))
        {
            // 直接改变当前形状的坐标
            var newCoord = CurrentShape.Position;
            newCoord.Y += 1;

            CurrentShape.Position = newCoord;
        }
        else
        {
            // 将当前的形状保存到地图中
            StorageShapeToMap();
        }

        // 扫描, 判断某些行可以被消除
        Scan();
    }
}
```


将地图渲染到控制台


```csharp
public void Render()
{
    StringBuilder sb = new StringBuilder();

    bool[,] mapCpy = new bool[Width, Height];
    Array.Copy(map, mapCpy, mapCpy.Length);

    if (CurrentShape != null)
    {
        foreach (var block in CurrentShape.GetBlocks())
        {
            Coordinate coord = Coordinate.GetAbstract(CurrentShape.Position, block);
            if (coord.X < 0 || coord.X >= Width ||
                coord.Y < 0 || coord.Y >= Height)
                continue;

            mapCpy[coord.X, coord.Y] = true;
        }
    }

    sb.AppendLine("┌" + new string('─', Width * 2) + "┐");
    for (int y = 0; y < Height; y++)
    {
        sb.Append("|");

        for (int x = 0; x < Width; x++)
        {
            sb.Append(mapCpy[x, y] ? "##" : "  ");
        }

        sb.Append("|");

        sb.AppendLine();
    }

    sb.AppendLine("└" + new string('─', Width * 2) + "┘");

    lock (this)
    {
        Console.SetCursorPosition(0, 0);
        Console.Write(sb.ToString());
    }
}
```
