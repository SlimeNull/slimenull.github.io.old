---
title: '[C#] 鼠标拖动实现控件移动 - 一个类实现对多个控件与窗体的鼠标拖动移动操作'
slug: '[CSharp]鼠标拖动实现控件移动-一个类实现对多个控件与窗体的鼠标拖动移动操作'
date: 2020-07-06T06:52:50+08:00
tags:
  - csharp
  - WinForm 
  - 实例 
  - 源码
categories:
  - 桌面程序
  - dotnet
description: '关于文章:文章包含以下部分:对鼠标拖动实现控件移动的原理详解使用类将功能封装适用于:C# WinForm原理:每当鼠标移动时, 根据鼠标坐标计算出控件应处于的位置并将控件移动到计算出的位置, 另外, 为了标识是否正在拖动控件, 还需要订阅控件的MouseDown和MouseUp事件.当MouseDown事件触发时, 标识是否正在拖动控件的布尔变量设置为true, 当MouseUp事件触发时, 标识是否正在拖动控件的布尔变量设置为false; 在鼠标移动时, 会判断这个变量的值以'
---


## 关于文章:

0. ###### 文章包含以下部分:
1. 对鼠标拖动实现控件移动的原理详解
2. 使用类将功能封装

## 适用于:

0. C# WinForm

> 更新日志: 2020-07-07 9:05AM 更改了多控件实现的算法, 使其能够支持移动窗体

## 原理:

0. 每当鼠标移动时, 根据鼠标坐标计算出控件应处于的位置并将控件移动到计算出的位置, 另外, 为了标识是否正在拖动控件, 还需要订阅控件的MouseDown和MouseUp事件.
1. 当MouseDown事件触发时, 标识是否正在拖动控件的布尔变量设置为true, 当MouseUp事件触发时, 标识是否正在拖动控件的布尔变量设置为false; 在鼠标移动时, 会判断这个变量的值以决定是否应该移动控件.
2. 为了保证鼠标相对控件的位置是不变的, 所以还需要一个Point类型的变量来标识鼠标按下控件时, 鼠标相对控件的相对位置.

## 一些资料:

0. 获取鼠标相对屏幕的坐标 :  Control.MousePosition;
1. 获取一个 相对于屏幕的坐标 相对某个控件的相对坐标 : 控件.PointToClient(一个相对于屏幕的坐标);
2.  ###### 下面是方便初学者的:
3. as运算符, 将某个实例作为某种类型使用, 例如:


```csharp
// obj是一个Button转换为object类型的按钮
// 那么, (obj as Button).Text = "新的文本"; 是可用的, 它将为控件指定一个新的文本
(obj as Button).Text = "新的文本";
// 注意, as运算符的优先级低于.运算符, 所以括号不可以省去
```

## 单控件实现:

0. 新建项目, 项目类型是WinForm.
1. 添加控件 (我这里将控件的Name设置为了ExempleButton)

> 浓浓的Chinglish味道, 哈哈哈


![添加按钮控件](images/20200706052109567.png)
3. 添加相关事件(MouseDown, MouseMove, MouseUp):

![添加相关事件](images/20200706052323882.png)
4. 转到代码, 添加相关变量.

![添加相关变量](images/20200706052556910.png)
5. 添加关键代码:

![代码图片](images/20200706054641301.png)


```csharp
bool Moving = false;                // 标识是否在拖动控件
Point MouseFirstLocation;           // 鼠标按下时, 相对于控件的坐标

private void ExempleButton_MouseDown(object sender, MouseEventArgs e)
{
    Moving = true;                                                   // 表示进入拖动控件的状态
    Point MousePoint = Control.MousePosition;                        // 获取鼠标相对屏幕的坐标
    MouseFirstLocation = ExempleButton.PointToClient(MousePoint);    // 获取坐标相对于控件的相对坐标并赋值给MouseFirstLocation
}

private void ExempleButton_MouseMove(object sender, MouseEventArgs e)
{
    if (Moving)
    {
        Point MousePoint = Control.MousePosition;                                                                                                   // 获取鼠标相对屏幕的坐标
        Point MousePointToContainer = ExempleButton.Parent.PointToClient(MousePoint);                                                               // 获取鼠标相对控件父容器的坐标
        Point ControlNewLocation = new Point(MousePointToContainer.X - MouseFirstLocation.X, MousePointToContainer.Y - MouseFirstLocation.Y);       // 计算控件应处于的, 新的坐标
        ExempleButton.Location = ControlNewLocation;                                                                                                // 移动控件
    }
}

private void ExempleButton_MouseUp(object sender, MouseEventArgs e)
{
    Moving = false;                                                  // 脱离拖动控件的状态
}
```

 - 可以使用了~

## 多控件实现:

- 使用类封装功能, 类的AddControl(Control control)方法来快捷使某个控件支持鼠标拖动移动位置.
- 这里的原理与前面的单控件实现有些出入, 但也是通过初始鼠标坐标(这里是相对父容器而不是移动的控件)与初始坐标计算出的
- 这个类不仅支持窗体内的控件, 而且还支持窗体 (没有直接使用WindowsAPI)

```csharp
class DragMove
{
    public DragMove(MouseButtons MouseButton)
    {
        this.MouseButton = MouseButton;
    }
    public DragMove()
    {
        AllMouseButton = true;
    }

    object AboutControl;                                  // 表示当前移动控件操作是针对哪个控件的
    bool Moving = false;                                  // 表示是否正在移动控件
    bool AllMouseButton = false;                          // 表示是否处理所有按钮
    Point ControlStartLocation;                           // 表示移动操作时, 控件的初始位置
    Point MouseFirstLocationAboutParent;                  // 表示移动操作时, 鼠标的初始位置
    MouseButtons MouseButton;
    List<Control> AddedControls = new List<Control>();

    void MouseDownEvent(object sender, MouseEventArgs e)
    {
        if ((e.Button == MouseButton) || AllMouseButton)
        {
            AboutControl = sender;
            Moving = true;
            ControlStartLocation = (sender as Control).Location;
            if (sender is Form)
            {
                MouseFirstLocationAboutParent = Control.MousePosition;
            }
            else
            {
                MouseFirstLocationAboutParent = (sender as Control).Parent.PointToClient(Control.MousePosition);
            }
        }
    }
    void MouseMoveEvent(object sender, MouseEventArgs e)
    {
        if (sender == AboutControl)
        {
            if (Moving)
            {
                Point MouseLocationNow;
                if (sender is Form)
                {
                    MouseLocationNow = Control.MousePosition;
                }
                else
                {
                    MouseLocationNow = (sender as Control).Parent.PointToClient(Control.MousePosition);
                }
                Point ControlNewLocation = new Point(MouseLocationNow.X - MouseFirstLocationAboutParent.X + ControlStartLocation.X, MouseLocationNow.Y - MouseFirstLocationAboutParent.Y + ControlStartLocation.Y);
                (sender as Control).Location = ControlNewLocation;
            }
        }
        else
        {
            if (Moving)
            {
                Moving = false;
                (AboutControl as Control).Location = ControlStartLocation;
            }
        }
    }
    void MouseUpEvent(object sender, MouseEventArgs e)
    {
        if (sender != AboutControl)
        {
            if (Moving)
            {
                (AboutControl as Control).Location = ControlStartLocation;
            }
        }
        Moving = false;
    }
    public bool AddControl(Control control)
    {
        if (AddedControls.Contains(control))
        {
            return false;
        }
        else
        {
            control.MouseDown += MouseDownEvent;
            control.MouseMove += MouseMoveEvent;
            control.MouseUp += MouseUpEvent;
            AddedControls.Add(control);
            return true;
        }
    }
    public bool RemoveControl(Control control)
    {
        if (AddedControls.Contains(control))
        {
            control.MouseDown -= MouseDownEvent;
            control.MouseMove -= MouseMoveEvent;
            control.MouseUp -= MouseUpEvent;
            AddedControls.Remove(control);
            return true;
        }
        else
        {
            return false;
        }
    }
}
```

- 这个类包含了判断鼠标按键的功能, 比如只允许右键拖动控件, 也有一些预防措施, 保证类能够正常工作的一些措施(判断控件), 也支持添加与移除控件

