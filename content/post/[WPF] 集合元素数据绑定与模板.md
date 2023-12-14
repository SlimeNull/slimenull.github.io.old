---
title: '[WPF] 集合元素数据绑定与模板'
slug: '[WPF]集合元素数据绑定与模板'
date: 2023-04-01 16:53:16
tags:
  - wpf
  - xaml
  - csharp
categories:
  - .NET
  - 桌面程序
description: '在 WPF 中使用 ItemsControl 进行集合数据的绑定'
---

在我们的应用程序里面, 很多时候会用到列表, 而列表元素的样式, 我们是希望能够自定义的, 根据 MVVM 设计模式, 我们的列表元素, 也需要绑定到后台数据, 这样后台数据进行更新的时候, 前台 UI 也会跟着更新, 包括元素也会自动增删.


## 使用 ItemsControl

ItemsControl 是最基本的 "多元素" 控件, 像是 ListBox, ListView 他都是基于 ItemsControl 的, 因为 ListBox 和 ListView 它们有一些默认样式. 例如 ListBox 它有一个滚动条, 所以当我们自定义的时候, 还是推荐使用 ItemsControl.


首先编写一个简单的数据模型:

```cs
public class StudentInfo
{
    public string Name { get; set; }
    public string Description { get; set; }

    public int Age { get; set; }
    public string Gender { get; set; }

    public int Grade { get; set; }
}
```


再写一个用于绑定数据的 **ViewModel**:

```cs
public class MainWindowModel
{
    public ObservableCollection<StudentInfo> StudentInfos { get; } = new ObservableCollection<StudentInfo>()
    {
        new StudentInfo()
        {
            Name = "IFdream",
            Description = "上课不听讲的学生",

            Age = 17,
            Gender = "Female",
            Grade = 114514
        },

        new StudentInfo()
        {
            Name = "ilyFairy",
            Description = "哼哼哼啊啊啊的学生",

            Age = 21,
            Gender = "武装直升机",
            Grade = 1919810
        }
    };
}
```


然后在窗口中随便写一个简单的界面:

```xml
<Window x:Class="WpfItemsBindingTutorial.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:vm="clr-namespace:WpfItemsBindingTutorial.ViewModels"
        xmlns:local="clr-namespace:WpfItemsBindingTutorial"
        mc:Ignorable="d"
        Title="MainWindow" Height="450" Width="800">
  <Window.DataContext>
    <!--直接给窗体的 DataContext 赋值-->
    <vm:MainWindowModel/>
  </Window.DataContext>
  <Grid>
    <Grid Margin="20" MaxWidth="400">
      <StackPanel>

        <!--使用 ItemsControl 进行集合的数据绑定, 在 ItemsSource 里面指定我们的集合-->
        <ItemsControl ItemsSource="{Binding StudentInfos}">
          <ItemsControl.ItemTemplate>

            <!--使用数据模板就可以为每一个元素自定义呈现方式了-->
            <DataTemplate>

              <!--整一个边框, 加背景颜色, 圆角, 以及内外边距-->
              <Border Margin="0 10 0 0" Padding="5" Background="#eeeeee" CornerRadius="5">

                <!--用 Effect 加个阴影-->
                <Border.Effect>
                  <DropShadowEffect ShadowDepth="0" BlurRadius="3"/>
                </Border.Effect>

                <!--这里用栈布局来做主要内容-->
                <StackPanel>

                  <!--在数据模板中, 可以直接绑定到我们的数据成员-->
                  <TextBlock>
                    <Run Text="Name:"/>
                    <Run Text="{Binding Name}"/>
                  </TextBlock>
                  <TextBlock>
                    <Run Text="Description:"/>
                    <Run Text="{Binding Description}"/>
                  </TextBlock>

                  <!--用 UniformGrid 做个三栏布局-->
                  <UniformGrid Columns="3">
                    <TextBlock>
                      <Run Text="Age:"/>
                      <Run Text="{Binding Age}"/>
                    </TextBlock>
                    <TextBlock>
                      <Run Text="Gender:"/>
                      <Run Text="{Binding Gender}"/>
                    </TextBlock>
                    <TextBlock>
                      <Run Text="Grade:"/>
                      <Run Text="{Binding Grade}"/>
                    </TextBlock>
                  </UniformGrid>
                </StackPanel>
              </Border>
            </DataTemplate>
          </ItemsControl.ItemTemplate>
        </ItemsControl>
      </StackPanel>
    </Grid>
  </Grid>
</Window>
```


于是我们就得到了这样的一个页面:

![效果预览](images/2c5aa4a7c76b4a1a910bf415c1d429b0.png)




## 使用 ListBox

事实上, `ListBox` 的使用和 `ItemsControl` 是差不多的, 包括同样的方式绑定 `ItemsSource`, 同样的方式设置 `ItemTemplate`, 只不过 `ListBox` 需要用户注意这些内容:


1. `ListBox` 的元素是可选中的, 如果你不希望元素可被选中, 那么使用 `ItemsControl`
2. `ListBox` 是自带滚动条的, 如果你希望自己控制滚动条, 你可以使用 `ScrollViewer.XXX` 附加属性来对它操作


`ListBox` 的元素是选中的, 这就意味着, 这个东西只适合一些数据的 "选择", 例如我要填一个表单, 然后给用户选择一个数据.  如果我要做一个聊天窗口, 聊天窗口里面的聊天消息, 显然是一个 "容器", 然后每一个消息气泡, 都是一个元素, 这个完全可以由绑定解决, 而使用 ListBox 会使消息可选中, 这不好, 我只是想让他自动生成控件, 所以使用 ItemsControl.


不过, 如果你要用 `ListBox` 做一个 *"导航栏"* 的话, 确实是一个不错的选择, 因为可以直接订阅 `SelectionChanged`, 并在事件中处理页面跳转的逻辑.
