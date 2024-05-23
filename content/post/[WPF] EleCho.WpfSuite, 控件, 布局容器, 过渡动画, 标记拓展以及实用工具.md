---
title: "[WPF] EleCho.WpfSuite, 控件, 布局容器, 过渡动画, 标记拓展以及实用工具"
slug: '20240523162351'
description: 关于 WPF Suite 的介绍与使用方法
date: 2024-05-23T16:23:51+08:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
---

相信各位朋友在搞 WPF 开发的时候，不可避免的会重复的写到很多重复的代码，以给WPF擦屁股。例如WPF中没有CornerRadiusAnimation，没有支持自定义的贝塞尔曲线过渡函数，内置的值转换器太少，控件无法设置圆角，容器不能直接通过自己的属性设置元素间距等诸多问题。

于是，我写了个库，用于补全我认为 WPF 中应该有，但是缺失了的一些功能。

## EleCho.WpfSuite

WPF 布局面板，控件，值转换器，标记拓展，过渡以及实用工具。

> 你可以在 Releases 中下载 SampleApp 看看这个库大概是什么样子的。
> https://github.com/OrgEleCho/EleCho.WpfSuite/releases


<br/>

### 安装与使用

在 nuget 中直接安装 EleCho.WpfSuite 即可。命名空间声明如下：

```xml
xmlns:ws="https://github.com/OrgEleCho/EleCho.WpfSuite"
```

库的所有功能均在这个命名空间下。

<br/>

### 布局面板

WpfSuite 中重写了 StackPanel 以及 WrapPanel，它们现在均支持使用 “Spacing” 属性来直接调整元素之间的间距，而不需要手动设置元素 Margin。WrapPanel 的间距是允许自定义水平以及垂直两个方向上的。

除此之外，WpfSuite 还提供了 FlexPanel（弹性面板），MasonryPanel（瀑布流面板）这两个布局容器。其中 FlexPanel 基本支持 CSS 中 flex 布局的所有功能，包括 Grow，Shrink 以及各种对齐。MasonryPanel 则是很直观的瀑布流布局，和 StackPanel 一样，你可以指定该容器的方向。而且这些布局控件都是支持使用 “Spacing” 属性来直接指定元素间距的。

![](images/Pasted%20image%2020240523162903.png)


<br/>

### 控件

WpfSuite 重写了大部分常用 WPF 控件，为它们直接添加了 CornerRadius 属性，以及其他关于控件状态的相关属性，例如 “DisabledBackground” 属性，当为其制定值时，如果控件被禁用，那么控件的背景颜色就会切换到设置的颜色。其他的状态还包含 Hover，Pressed，Selected，Selected Inactive，这样我们不需要更改控件的模板，就能直接更改它在不同状态下的颜色，边框。

很多时候我们会遇到 “控件” 的内容超出带圆角的 “Border” 范围的情况，我们希望控件的内容被裁剪到 Border 内部，现在这个需求可以非常简单的实现。WpfSuite 重写了 Border 的实现，并提供了一个 “ContentClip” 属性，只需要把 Border 的内容的 Clip 属性绑定到 Border 的 ContentClip 上，就可以完成自动裁剪了！

而且，所有 WpfSuite 提供的控件均不影响原有 WPF 控件，它不会对原有 WPF 控件做任何更改，所以如果你担心某些控件的拓展功能会造成性能问题，你也可以完全不使用它。

![](images/Pasted%20image%2020240523162925.png)

<br/>

### 值转换器

WpfSuite 还提供了常用的诸多值转换器，例如用于将任意对象值判空并返回 bool 的转换器，将数字与指定数字做对比并返回 bool 的转换器，做一些简单的数学计算并返回结果的 bool 转换器，以及能够将值转换器串起来的 “值转换器组”。

它们能够做的东西当然超级多，例如，如果你有一个属性是集合类型的，那么你完全可以将界面上某元素的 Visibility 绑定到这个属性的 Count 属性上，然后指定转换器为 “数字非0判断” 以及 Bool 转 Visibility 组合成的一个 ValueConverterGroup，于是，这个控件就仅在集合不为空的时候会显示出来了！这很 MVVM，这很酷！

```xml
<ws:ValueConverterGroup x:Key="CollectionIsNotEmptyToVisibilityConverter">
    <ws:CollectionIsNotNullOrEmptyConverter/>
    <ws:BooleanToVisibilityConverter/>
</ws:ValueConverterGroup>
```


![](images/Pasted%20image%2020240523162944.png)

<br/>

### 标记拓展

标记拓展应该是 WpfSuite 最简单的部分了，它只是提供了 MarkupExtension 以让你能在 XAML 中直接写出来表示具体类型的值。

例如，你有一个控件，该控件有一个属性是 Object 类型的，你希望往里面传入一个长整型，如果你在 XAML 中直接写 “114514” 这样的数字，它肯定不会帮你转成你想要的类型并传入的，它会把你的字符串原封不动的传过去… 这个时候，标记拓展就派上用场了！你直接使用 {ws:Int64 114514} 就能直接表示一个精确的长整型的值。

还有一些使用场景，就是 DataTrigger，例如你希望你的某些数据触发器，当数据等于某个确切值得时候触发，但很遗憾， DataTrigger 的 Value 属性正好是 Object 类型的，而有些时候它并不能识别到你这个数据的类型，也当然没办法对你在 XAML 中写的字符串做正确的转型，这个时候用能够制定确切类型的标记拓展再合适不过了。

<br/>

### 过渡

过渡在应用程序中用的应该是特别多的，我们很难想象，WPF 提供了完整的动画系统，但是居然没有提供封装好的过渡动画！

有时候，我们希望某个内容被更改的时候，它能够执行一些动画，例如淡入淡出，划出划入，或者是缩放，甚至旋转，或把这些结合起来之类的，这个时候，WpfSuite 提供的 TransitioningContentControl 能够轻松的帮你解决这个问题！

> 熟悉 Avalonia 的朋友肯定要说了，这不就 Avalonia 自带的东西嘛！没错！我就是看到 Avalonia 有这东西，但是 WPF 没有，然后我眼红，就移植过来了！

对 TransitioningContentControl 指定你想要的过渡效果，它就可以在内容变更时自动执行这个效果。内置的有 Slide（滑动），Fade（淡入淡出），Scale（缩放）以及结合 Slide 和 Fade 的 SlideFade（滑动的同时，淡入淡出）。

而且我们对 Frame 控件也支持了过渡动画，又因为动画也是有正向与反向之分的，所以当你使用 Frame 的 ”GoBack“ 方法时，动画的执行也是反向的。

![](images/Pasted%20image%2020240523164142.png)


同时，你也可以手动将动画的 Reverse 设为 true，这样它的方向就被置换了。最经典的一个应用就是，左侧导航项与右侧带滑动动画的 Frame，当点击当前项后面的项时，正向执行动画，当点击当前项前面的项时，反向执行动画，这样你就做到了一个非常不错的页面滑动效果！

![](images/Pasted%20image%2020240523164633.png)

![](images/Pasted%20image%2020240523164706.png)

除此之外，你也可以用它来做 Banner 图像的切换动画，例如用户点击按钮的时候，更换图片，而图片由 TransitioningContentControl 容纳，所以也可与用它来做这个动画，点击左边的按钮则从左方滑入新图片，点击右边的按钮则从右方滑入新图片，听起来就很不错。

![](images/Pasted%20image%2020240523181634.png)

<br/>

### 实用工具

在这部分中，最有用的就属绑定代理了。举个例子，我需要做一个列表项的右键菜单，并且菜单使用 MVVM，也就是说，逻辑使用指令实现，参数通过绑定传入。但是右键菜单的可视化树和列表不是同一个，你在 Menu 上使用 Binding 会因为找不到 DataContext 而失败。

使用绑定代理则可以轻松解决这个问题。因为静态资源是正常使用的，所以你只需要把绑定代理作为静态资源定义在窗口中，并指定 Data="{Binding}"，然后在你的 Menu 中把 Binding 的 DataSource 指定为刚刚定义的静态资源，就能够成功在菜单中引用到正常的 DataContext 了。

![](images/Pasted%20image%2020240523163503.png)

> 该方案适用于任何因 VisualTree 而导致 DataContext 找不到，进而导致绑定失败的所有问题。

除此之外，还有一些小工具，例如 ValidationUtils，用于自定义依赖属性的属性验证，或者 ColorUtils，用于计算 HSV 与 RGB 的转换。
