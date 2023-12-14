---
title: '[C#] 一个类实现拖拽调整窗体或控件大小'
slug: '[CSharp]一个类实现拖拽调整窗体或控件大小'
date: 2020-07-10 03:11:28
categories:
  - 类库
  - 桌面程序
  - dotnet
description: '最近闲来无事, 倒是借助WebAPI实现翻译器, 本想设计一个炫酷的界面(模仿VS), 却没想到, 难度大大超出我的想象, 拖拽, 调整大小, 如果要实现VS的边框, 还需要想办法做到过渡透明! 这对于WinForm来说实在是太难了, 如果不过渡透明, 就是全透明, 那鼠标就直接穿窗体了!不过还是有些成果的, 比如, 造了两个轮子(我真是一个热衷于造轮子的傻子)所说的轮子就是文章标题咯, 因为之前我还做了一个类来实现拖拽移动控件或窗体嘛, 所以我就直接把这个调整大小的跟之前的功能整合到了..'
---

![在这里插入图片描述](images/2020071002573582.png)


0. 最近闲来无事, 倒是借助WebAPI实现翻译器, 本想设计一个炫酷的界面(模仿VS), 却没想到, 难度大大超出我的想象, 拖拽, 调整大小, 如果要实现VS的边框, 还需要想办法做到过渡透明! 这对于WinForm来说实在是太难了, 如果不过渡透明, 就是全透明, 那鼠标就直接穿窗体了!
1. 不过还是有些成果的, 比如, 造了两个轮子 


> (我真是一个热衷于造轮子的傻子)


2. 所说的轮子就是文章标题咯, 因为之前我还做了一个类来实现拖拽移动控件或窗体嘛, 所以我就直接把这个调整大小的跟之前的功能整合到了一起. (可以提到的是, 期间因为特殊需求, 还做了一个映射拖拽移动控件窗体, 就是拖拽控件1, 移动控件2)

3. 下面的代码就是程序主体了, 它是应该被封装到一个类库里的, 所以, 不要直接把这些代码添加到你的文件中, 而是新建一个类库(不是新建项目, 而是添加到你的项目中), 然后把这些代码覆盖到新的文件中, 在你的程序中using CHO.DragOperation即可使用代码所包含的3个功能( ~DragMove,MapDragMove,DragResize~ )


```csharp
using System;
using System.Collections.Generic;
using System.Drawing;
using System.Windows.Forms;

namespace CHO
{
    namespace DragOperation
    {
        public class DragMove
        {
            public DragMove(MouseButtons MouseButton, DragMoveMode FollowMode = DragMoveMode.All, bool OutParent = false)
            {
                this.MouseButton = MouseButton;
                this.FollowMode = FollowMode;
                this.OutParent = OutParent;
            }
            public DragMove(DragMoveMode FollowMode = DragMoveMode.All, bool OutParent = false)
            {
                AllMouseButton = true;
                this.FollowMode = FollowMode;
                this.OutParent = OutParent;
            }

            object AboutControl;                                  // 表示当前移动控件操作是针对哪个控件的
            bool Moving = false;                                  // 表示是否正在移动控件
            bool AllMouseButton = false;                          // 表示是否处理所有按钮
            bool OutParent;                                       // 表示是否允许脱离父容器的显示区域
            Point ControlStartLocation;                           // 表示移动操作时, 控件的初始位置
            Point MouseFirstLocationAboutParent;                  // 表示移动操作时, 鼠标的初始位置
            DragMoveMode FollowMode;                              // 跟随模式
            MouseButtons MouseButton;                             // 判定按钮
            List<Control> AddedControls = new List<Control>();    // 已添加控件


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
                        int NewX = MouseLocationNow.X - MouseFirstLocationAboutParent.X + ControlStartLocation.X;
                        int NewY = MouseLocationNow.Y - MouseFirstLocationAboutParent.Y + ControlStartLocation.Y;
                        if (!OutParent)
                        {
                            if (!(sender is Form))
                            {
                                if (NewX < 0)
                                {
                                    NewX = 0;
                                }
                                else if (NewX + (sender as Control).Width > (sender as Control).Parent.Width)
                                {
                                    NewX = (sender as Control).Parent.Width - (sender as Control).Width;
                                }
                                if (NewY < 0)
                                {
                                    NewY = 0;
                                }
                                else if (NewY + (sender as Control).Height > (sender as Control).Parent.Height)
                                {
                                    NewY = (sender as Control).Parent.Height - (sender as Control).Height;
                                }
                            }
                        }
                        if (FollowMode == DragMoveMode.X)
                        {
                            NewY = ControlStartLocation.Y;
                        }
                        if (FollowMode == DragMoveMode.Y)
                        {
                            NewX = ControlStartLocation.X;
                        }
                        (sender as Control).Location = new Point(NewX, NewY);
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

        public class MapDragMove
        {
            public MapDragMove(MouseButtons MouseButton, DragMoveMode FollowMode = DragMoveMode.All)
            {
                this.MouseButton = MouseButton;
                this.FollowMode = FollowMode;
            }
            public MapDragMove(DragMoveMode FollowMode = DragMoveMode.All)
            {
                AllMouseButton = true;
                this.FollowMode = FollowMode;
            }

            object AboutControl;                                  // 表示当前移动控件操作是针对哪个控件的
            bool Moving = false;                                  // 表示是否正在移动控件
            bool AllMouseButton = false;                          // 表示是否处理所有按钮
            Point ControlStartLocation;                           // 表示移动操作时, 控件的初始位置
            Point MouseFirstLocation;                             // 表示移动操作时, 鼠标的初始位置
            DragMoveMode FollowMode;                              // 跟随模式
            MouseButtons MouseButton;                             // 判定按钮
            Dictionary<Control, Control> AddedControls = new Dictionary<Control, Control>();    // 已添加控件


            void MouseDownEvent(object sender, MouseEventArgs e)
            {
                if ((e.Button == MouseButton) || AllMouseButton)
                {
                    AboutControl = sender;
                    Moving = true;
                    ControlStartLocation = (sender as Control).Location;
                    MouseFirstLocation = Control.MousePosition;
                }
            }
            void MouseMoveEvent(object sender, MouseEventArgs e)
            {
                if (sender == AboutControl)
                {
                    if (Moving)
                    {
                        Point MouseLocationNow = Control.MousePosition;

                        if (FollowMode == DragMoveMode.X)
                        {
                            AddedControls[sender as Control].Left += MouseLocationNow.X - MouseFirstLocation.X;
                        }
                        else if (FollowMode == DragMoveMode.Y)
                        {
                            AddedControls[sender as Control].Top += MouseLocationNow.Y - MouseFirstLocation.Y;
                        }
                        else
                        {
                            AddedControls[sender as Control].Location = new Point(AddedControls[sender as Control].Location.X + (MouseLocationNow.X - MouseFirstLocation.X), AddedControls[sender as Control].Location.Y + (MouseLocationNow.Y - MouseFirstLocation.Y));
                        }

                        MouseFirstLocation = MouseLocationNow;
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
            public bool AddControl(Control dragControl, Control moveControl)
            {
                if (AddedControls.ContainsKey(dragControl))
                {
                    return false;
                }
                else
                {
                    dragControl.MouseDown += MouseDownEvent;
                    dragControl.MouseMove += MouseMoveEvent;
                    dragControl.MouseUp += MouseUpEvent;
                    AddedControls.Add(dragControl, moveControl);
                    return true;
                }
            }
            public bool RemoveControl(Control control)
            {
                if (AddedControls.ContainsKey(control))
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

        /// <summary>
        /// 控件跟随鼠标的移动模式
        /// </summary>
        public enum DragMoveMode
        {
            /// <summary>
            /// 表示只跟随横坐标
            /// </summary>
            X,
            /// <summary>
            /// 表示只跟随纵坐标
            /// </summary>
            Y,
            /// <summary>
            /// 跟随横坐标与纵坐标
            /// </summary>
            All
        }

        public class DragResize
        {
            public DragResize(MouseButtons MouseButton, int BorderSize = 5)
            {
                this.MouseButton = MouseButton;
                this.BorderSize = BorderSize;
            }
            public DragResize(int BorderSize = 5)
            {
                this.AllMouseButton = true;
                this.BorderSize = BorderSize;
            }

            int BorderSize = 10;                                  // 边框判断大小
            object AboutControl;                                  // 表示当前调整控件操作是针对哪个控件的
            bool Resizing = false;                                // 表示是否正在调整控件
            bool AllMouseButton = false;                          // 表示是否处理所有按钮

            bool MouseDownING = false;

            bool LeftResize;
            bool RightResize;
            bool TopResize;
            bool BottomResize;
            private Point MouseFirstLocation;                     // 表示调整操作时, 鼠标的初始位置
            MouseButtons MouseButton;                             // 判定按钮
            List<Control> AddedControls = new List<Control>();    // 已添加控件

            void CheckMouse(object sender)
            {
                Point mousePosition;
                if (sender is Form)
                {
                    mousePosition = Control.MousePosition;
                }
                else
                {
                    mousePosition = (sender as Control).Parent.PointToClient(Control.MousePosition);
                }

                if (mousePosition.X >= (sender as Form).Left && mousePosition.X <= (sender as Form).Left + BorderSize)
                {
                    LeftResize = true;
                }
                else if (mousePosition.X >= (sender as Form).Left + (sender as Form).Width - BorderSize && mousePosition.X <= (sender as Form).Left + (sender as Form).Width)
                {
                    RightResize = true;
                }
                else
                {
                    LeftResize = false;
                    RightResize = false;
                }
                if (mousePosition.Y <= (sender as Form).Top + BorderSize && mousePosition.Y >= (sender as Form).Top)
                {
                    TopResize = true;
                }
                else if (mousePosition.Y >= (sender as Form).Top + (sender as Form).Height - BorderSize && mousePosition.Y <= (sender as Form).Top + (sender as Form).Height)
                {
                    BottomResize = true;
                }
                else
                {
                    TopResize = false;
                    BottomResize = false;
                }

                if ((LeftResize || RightResize || TopResize || BottomResize) && MouseDownING)
                {
                    Resizing = true;
                    AboutControl = sender;
                }
                else
                {
                    Resizing = false;
                }
            }

            void MouseDownEvent(object sender, MouseEventArgs e)
            {
                if (AllMouseButton || e.Button == MouseButton)
                {
                    MouseDownING = true;
                    CheckMouse(sender);

                    if (Resizing)
                    {
                        MouseFirstLocation = Control.MousePosition;
                    }
                }
            }
            void MouseMoveEvent(object sender, MouseEventArgs e)
            {
                if (Resizing && sender == AboutControl)
                {
                    Point mousePosition = Control.MousePosition;
                    (sender as Control).Invalidate(false);
                    if (LeftResize)
                    {
                        (sender as Control).Width += MouseFirstLocation.X - mousePosition.X;
                        (sender as Control).Left -= MouseFirstLocation.X - mousePosition.X;
                    }
                    else if (RightResize)
                    {
                        (sender as Control).Width += mousePosition.X - MouseFirstLocation.X;
                    }
                    if (TopResize)
                    {
                        (sender as Control).Height += MouseFirstLocation.Y - mousePosition.Y;
                        (sender as Control).Top -= MouseFirstLocation.Y - mousePosition.Y;
                    }
                    else if (BottomResize)
                    {
                        (sender as Control).Height += mousePosition.Y - MouseFirstLocation.Y;
                    }
                    (sender as Control).Invalidate(true);
                    MouseFirstLocation = mousePosition;
                }
                else
                {
                    CheckMouse(sender);
                    if (TopResize)
                    {
                        if (LeftResize)
                        {
                            (sender as Control).Cursor = Cursors.SizeNWSE;
                        }
                        else if (RightResize)
                        {
                            (sender as Control).Cursor = Cursors.SizeNESW;
                        }
                        else
                        {
                            (sender as Control).Cursor = Cursors.SizeNS;
                        }
                    }
                    else if (BottomResize)
                    {
                        if (LeftResize)
                        {
                            (sender as Control).Cursor = Cursors.SizeNESW;
                        }
                        else if (RightResize)
                        {
                            (sender as Control).Cursor = Cursors.SizeNWSE;
                        }
                        else
                        {
                            (sender as Control).Cursor = Cursors.SizeNS;
                        }
                    }
                    else if (LeftResize)
                    {
                        (sender as Control).Cursor = Cursors.SizeWE;
                    }
                    else if (RightResize)
                    {
                        (sender as Control).Cursor = Cursors.SizeWE;
                    }
                    else
                    {
                        (sender as Control).Cursor = Cursors.Default;
                    }

                }
            }
            void MouseUpEvent(object sender, MouseEventArgs e)
            {
                MouseDownING = false;
                if (Resizing)
                {
                    Resizing = false;
                }
            }
            void MouseLeaveEvent(object sender, EventArgs e)
            {
                (sender as Control).Cursor = Cursors.Default;
            }

            public bool AddControl(Control control)
            {
                if (AddedControls.Contains(control))
                {
                    return false;
                }
                else
                {
                    control.MouseLeave += MouseLeaveEvent;
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
    }
}

```

