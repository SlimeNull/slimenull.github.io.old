---
title: '[WPF] 资源, 静态资源, 动态资源的使用及注意事项. 使用资源字典进行 “语言国际化“ 与 “颜色模式切换“'
slug: '20230403133618'
date: 2023-04-03T13:36:18+08:00
tags:
  - wpf
  - ui
  - csharp
categories:
  - 桌面程序
  - dotnet
description: '资源, 静态与动态资源, 基于资源实现语言国际化及配色切换'
---

资源是 WPF 里面的一个好东西, 你可以在里面存很多东西, 然后直接在 XAML 或 CS 代码中调用获取对应的值. 任何东西都可以存进去, 只要它是可以由文本表示的, 那就可以.


## 定义资源的方式

资源定义有很多方式, 它们也有不同的效果, 作用域, 以及合适的用途.


### 1. 在控件中定义资源


在 WPF 中, 任何一个 `FrameworkElement` (框架元素) 都可以用来定义资源, 它有一个 `Resources` 属性, 我们使用它来定义所需资源即可.


在一个控件中定义的资源, 这个资源也只能在这个控件中被访问, 外部是无法访问到这个资源的.


下面是一个在 `StackPanel` 中定义一个适用于 `Button` 的 `Style`:


```xml
<StackPanel>
  <StackPanel.Resources>
    <Style TargetType="Button">
      <Setter Property="Margin" Value="3"/>
    </Style>
  </StackPanel.Resources>
  
  <Button>Click</Button>
  <Button>Me</Button>
  <Button>To</Button>
  <Button>Go</Button>
</StackPanel>
```


在上面的代码中, `StackPanel` 控件的 `Resources` 中定义了一个 `Style`, 这个 `Style` 的目标类型是 `Button`, 它将应用于 `StackPanel` 下所有的 `Button`, 例如在上面代码中, 我们为按钮定义了 `Margin` 值为 3, 这样, 这个 `StackPanel` 中的所有 `Button` 都可以快速的空出一些间距, 而不需要我们为每一个 `Button` 赋值 `Margin` 了.


效果图:

![效果图](images/c8787655929448d88d727980e975e2ae.png)



很多时候, 我们也在 `Window` 或 `Page` 中定义一些资源, 例如转换器, 某些必要的数据之类的, 这些资源在整个 `Window` 和 `Page` 都能访问, 而不会被外面访问到, 非常方便.


```xml
<Page x:Class="OpenGptChat.Views.MainPage" Title="MainPage"
      xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
      xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
      xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006" 
      xmlns:d="http://schemas.microsoft.com/expression/blend/2008" 
      xmlns:local="clr-namespace:OpenGptChat.Views"
      xmlns:utilities="clr-namespace:OpenGptChat.Utilities;assembly=OpenGptChat.Common"
      mc:Ignorable="d" d:DesignHeight="600" d:DesignWidth="880" d:Background="White"
      d:DataContext="{d:DesignInstance Type=local:MainPage}"
      Background="{DynamicResource GeneralBackground}" Style="{DynamicResource AnimatedPageStyle}">
    <Page.Resources>
        <utilities:BindingProxy x:Key="PageSelf" Data="{Binding}"/>
    </Page.Resources>

    <!--省略页面内容-->
</Page>
```


### 2. 在资源字典中定义资源


如果你翻了 `FrameworkElement` 的定义, 你会发现, 它的 `Resources` 属性的类型, 就是 `ResourceDictionary`, 即: 资源字典.


在 WPF 中, 你同样可以在你的项目中创建一个资源字典文件, 它是一个 XAML 文件, 大概是这样的格式:


```xml
<ResourceDictionary xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
                    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">
                    
  <!--在这里定义资源-->
  
</ResourceDictionary>
```


如果你想在别的地方引用一个资源字典, 可以直接在你的 XAML 中这么声明:


```xml
<ResourceDictionary Source="资源字典的路径"/>
```


因为刚刚的 `FrameworkElement` 的 `Resources` 属性是 `ResourceDictionary` 类型, 所以你也可以直接把自己的资源字典赋值过去, 大概是这样的:


```xml
<StackPanel>
  <StackPanel.Resources>
    <ResourceDictionary Source="资源字典的路径"/>
  </StackPanel.Resources>
</StackPanel>
```


如果你希望定义一些全局都能使用的资源, 那么你可以直接在项目中的 `App.xaml` 中定义. 在 `App.xaml` 定义的资源, 是整个程序都可以直接访问到的, 例如这样:


```xml
<Application x:Class="WpfTutorial.App"
             xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:local="clr-namespace:WpfTutorial"
             StartupUri="MainWindow.xaml">
  <Application.Resources>
    <Style TargetType="Button" x:Key="pinkButton">
      <Setter Property="Background" Value="Pink"/>
    </Style>
  </Application.Resources>
</Application>
```


需要知道的是, 资源字典也是可以合并的, `ResourceDictionary` 有一个属性叫做 `MergedDictionaries`, 你可以在这里添加一些其他的资源字典, 然后那些资源字典中包含的资源, 就会合并到当前字典中.


`OpenGptChat` 的 `App.xaml` 是这样定义的:

```xml
<Application x:Class="OpenGptChat.App"
             xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:local="clr-namespace:OpenGptChat">
    <Application.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <ResourceDictionary Source="/Themes/Generic.xaml"/>
                <ResourceDictionary Source="/Resources/AnimationStyles.xaml"/>
                <ResourceDictionary Source="/Resources/ControlStyles/All.xaml"/>
                <ResourceDictionary Source="/Converters/SingletonConverters.xaml"/>

                <ResourceDictionary Source="pack://application:,,,/OpenGptChat.Common;component/ColorModes/BrightMode.xaml"/>
                <ResourceDictionary Source="pack://application:,,,/OpenGptChat.Common;component/Languages/en.xaml"/>
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```


从上面的代码中我们可以看到, 它没有直接在 `App.xaml` 中直接定义任何资源, 而是在其他的地方定义资源, 最后将它们合并到 `App.xaml`, 这样将资源分门别类, 管理起来也是方便的.


## 访问资源的方式


在上面的内容中, 你应该也看到了, 我直接在控件中定义的 `Style`, 它有一个 `TargetType` 属性, 或者我在上面 `App.xaml` 中定义 `Style` 的时候, 添加了一个 `x:Key` 属性. 是这些决定了资源的访问方式.


### 1. 访问使用目标类型定义的资源


如果一个资源是一个 `Style`, 那么它可以直接有一个 `TargetType` 属性, 当你给它赋值后, 所有能访问到该资源的, 对应类型的控件, 都会应用这个样式, 换言之, 你可以用它定义一些 *全局* 或 *通用* 的样式, 然后应用到你想要的地方.


上面直接在 `StackPanel` 中定义 `Style` 资源的例子就是使用的这种方式, 它只有一个 `TargetType`, 于是它的成员中, 所有的 `Button` 都会应用这个样式, 然后设置 `Margin` 值为样式中指定的值.


如果你希望将一个样式应用到整个应用程序, 很简单, 只需要让它可以被整个应用程序访问即可, 那么我们应该用什么呢? 当然是 `App.xaml` 了. 你在 `App.xaml` 中直接定义的 `Style` 资源或者合并到 `App.xaml` 的 `Style` 资源, 如果设置了 `TargetType` 并且没有设置 `x:Key`, 那么它们就会对全局进行应用.


### 2. 访问使用键定义的资源


任何一个资源, 如果你定义了键, 也就是 `x:Key`, 那么访问它的时候, 就只能通过 `{StaticResource 键}` 或 `{DynamicResource 键}` 来访问.


它们分别是静态资源和动态资源. 作用就和它们的名字一样, 静态资源一旦引用之后, 就不会变更, 而使用动态资源引用的资源, 如果你动态更新了资源字典, 那么这个引用也会更新.


通过这个特性, 我们可以用来做 "语言国际化", 也就是给程序添加多语言支持. 同理, 如果你要做程序的颜色模式切换, 那么也可以直接做, 毕竟, 控件的 `Foreground`, `Background` 以及几乎任何东西都是可以通过动态资源来引用的.


## 动态切换资源


只要你的 CS 代码能访问到的地方, 你都可以操作. 以 `App.xaml` 为例, 它其实是一个类, 基类型是 `Application`, 而我们在 `App.xaml` 中访问到的属性, 基本都是在访问 `Application` 的属性.


### 1. 基本操作


如果你翻过 `Application` 的定义, 你会发现, 它提供了一个 `Current` 属性, 用来获取当前正在运行的 `Application` 实例.


下面是操作 `Application` 替换当前程序的资源的示例代码.


```cs
ResourceDictionary res = 我的资源字典, 这里怎么获取到的你自己决定咯;

// 直接将当前应用程序的资源替换成我们的资源
Application.Current.Resources = res;
```

或者委婉一点, 我们也可以在当前程序资源字典中的 `MergedDictionaries` 中添加自己的资源, 它可以直接覆盖掉相同 `x:Key` 的值.


```cs
ResourceDictionary res = 我的资源字典, 这里怎么获取的你自己决定咯;

// 在当前应用程序的资源中, 添加我们自己的资源.
Application.Current.Resources.MergedDictionaries.Add(res);
```


### 2. 删掉旧的, 添加新的


直接替换掉程序的资源字典显然不好, 因为我们里面可能定义了很多东西, 更换起来不方便. 而直接向里面添加新的, 覆盖旧的也不好, 因为久而久之, 资源字典会越积越多, 可能会影响程序的性能.


想要删去旧的字典, 首先我们就得知道这个字典是不是我们所要删去的字典. 例如我程序资源的合并字典中有一个用来存放 *语言* 的字典, 那么做得让它有个地方能够标识出 "这个字典是用来存放语言信息的". 最简单的方式就是在字典中加入一个带有标识性质的资源, 例如这样:


```xml
<ResourceDictionary xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
                    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
                    xmlns:s="clr-namespace:System;assembly=mscorlib">
    
    <!-- 这里存放资源 -->
    
    <!-- 用一个名称不会和其他资源字典资源重复的任意资源做标识 -->
    <s:Boolean x:Key="IsLanguageResource">True</s:Boolean>
</ResourceDictionary>
```


在判断的时候这样写:


```cs
var oldResources = 
    Application.Current.Resources.MergedDictionaries
        .Where(res => res.Contains("IsLanguageResource")
        .ToList();
```

下面的代码取自 [OpenGptChat](https://github.com/SlimeNull/OpenGptChat) 的 `ColorModeService`, 可以做动态资源切换的参考:


```cs
// 切换资源到指定资源字典
private void SwitchTo(ResourceDictionary colorModeResource)
{
    // 拿到旧的资源
    var oldColorModeResources =
        Application.Current.Resources.MergedDictionaries
            .Where(dict => dict.Contains("IsColorModeResource"))
            .ToList();

    // 删掉旧的资源
    foreach (var res in oldColorModeResources)
        Application.Current.Resources.MergedDictionaries.Remove(res);

    // 添加新的资源
    Application.Current.Resources.MergedDictionaries.Add(colorModeResource);
}
```


## 国际化与配色切换


WPF 中的界面可以绑定到资源, 而资源切换后界面也会跟着更新, 利用这个特点, 我们可以将要显示的文本绑定到我们的语言资源, 将界面中前景色, 背景色等各种控件的颜色绑定到我们的配色资源, 然后动态切换资源, 即可做到国际化与配色切换.


### 1. 编写多个资源文件


做国际化的时候, 为了避免资源找不到的情况, 我们还需要一个 *fallback* 的情况. 因为资源是可覆盖的, 所以我们使用以下逻辑:


1. 定义一个 `_base.xaml`, 在里面编写一些基础的资源, 以及默认语言的一些文本.
2. 定义其他语言, 例如 `zh-Hans.xaml`, 然后在 `MergedDictionaries` 中添加 `_base.xaml`, 这样, 雀食的内容会 *fallback* 到 `_base.xaml` 中的资源, 而重复的资源则会进行覆盖.


下面是来自 [OpenGptChat](https://github.com/SlimeNull/OpenGptChat) 的, 可参考的  `_base.xaml`:


```xml
<ResourceDictionary xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
                    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
                    xmlns:s="clr-namespace:System;assembly=mscorlib">
    
    <!--页面名-->
    <s:String x:Key="StrChat">Chat</s:String>
    <s:String x:Key="StrConfiguration">Configuration</s:String>

    <!--配置名-->
    <s:String x:Key="StrAPIKey">API Key</s:String>
    <s:String x:Key="StrAPIGPTModel">API GPT Model</s:String>
    <s:String x:Key="StrAPIHost">API Host</s:String>
    <s:String x:Key="StrAPITimeout">API Timeout</s:String>
    <s:String x:Key="StrTemperature">Temperature</s:String>
    <s:String x:Key="StrSystemMessages">System messages</s:String>
    <s:String x:Key="StrApply">Apply</s:String>
    <s:String x:Key="StrWindowAlwaysOnTop">Window always on top</s:String>

    <s:String x:Key="StrLanguage">Language</s:String>
    <s:String x:Key="StrColorMode">Color mode</s:String>

    <!--热键描述-->
    <s:String x:Key="StrHotkeyTips">Hotkety Tips</s:String>
    <s:String x:Key="StrCloseApplication">Close application</s:String>
    <s:String x:Key="StrHideApplication">Hide application</s:String>
    <s:String x:Key="StrShowApplication">Show application</s:String>
    <s:String x:Key="StrSendMessage">Send message</s:String>

    <s:String x:Key="StrGlobalHotkey">Global hotkey</s:String>
    <s:String x:Key="StrInputBox">Input box</s:String>

    <!--按钮-->
    <s:String x:Key="StrSend">Send</s:String>
    <s:String x:Key="StrSave">Save</s:String>
    <s:String x:Key="StrNewSession">New Session</s:String>

    <!--工具提示-->
    <s:String x:Key="StrAboutOpenGptChat">About OpenGptChat</s:String>
    <s:String x:Key="StrGoBackToMainPage">Go back to Main page</s:String>
    <s:String x:Key="StrResetChat">Reset chat</s:String>
    <s:String x:Key="StrGoToConfigurationPage">Go to Configuration page</s:String>
    
    <!--菜单相关-->
    <s:String x:Key="StrCopy">Copy</s:String>
    <s:String x:Key="StrEdit">Edit</s:String>
    <s:String x:Key="StrDelete">Delete</s:String>
    
    <!--最后使用一个 'IsLanguageResource' 标识当前资源字典是语言资源-->
    <s:Boolean x:Key="IsLanguageResource">True</s:Boolean>
</ResourceDictionary>
```


可以看到, 上面的内容中, 我们使用的是英文, 所以当我们编写 `en.xaml` 的时候, 只需要直接 *fallback*, 而不需要重新写内容:


```xml
<ResourceDictionary xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
                    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
                    xmlns:s="clr-namespace:System;assembly=mscorlib">
    <ResourceDictionary.MergedDictionaries>
        <ResourceDictionary Source="_base.xaml"/>
    </ResourceDictionary.MergedDictionaries>

    <!--英语是默认语言, 直接 fallback 过去-->
    
</ResourceDictionary>
```


但是, 如果是中文的话, 我们就需要进行覆盖了:


```xml
<ResourceDictionary xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
                    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
                    xmlns:s="clr-namespace:System;assembly=mscorlib">
    <ResourceDictionary.MergedDictionaries>
        <ResourceDictionary Source="_base.xaml"/>
    </ResourceDictionary.MergedDictionaries>
    
    <!--页面名称-->
    <s:String x:Key="StrChat">聊天</s:String>
    <s:String x:Key="StrConfiguration">配置</s:String>

    <!--配置名称-->
    <s:String x:Key="StrAPIKey">API 密钥</s:String>
    <s:String x:Key="StrAPIHost">API 主机</s:String>
    <s:String x:Key="StrAPIGPTModel">API GPT 模型</s:String>
    <s:String x:Key="StrAPITimeout">API 超时时间</s:String>
    <s:String x:Key="StrTemperature">温度</s:String>
    <s:String x:Key="StrSystemMessages">系统消息</s:String>
    <s:String x:Key="StrApply">应用</s:String>
    <s:String x:Key="StrWindowAlwaysOnTop">窗口置顶</s:String>

    <s:String x:Key="StrLanguage">语言</s:String>
    <s:String x:Key="StrColorMode">色彩模式</s:String>

    <!--热键描述-->
    <s:String x:Key="StrHotkeyTips">热键提示</s:String>
    <s:String x:Key="StrCloseApplication">关闭程序</s:String>
    <s:String x:Key="StrHideApplication">隐藏程序</s:String>
    <s:String x:Key="StrShowApplication">显示程序</s:String>
    <s:String x:Key="StrSendMessage">发送消息</s:String>

    <s:String x:Key="StrGlobalHotkey">全局热键</s:String>
    <s:String x:Key="StrInputBox">输入框</s:String>


    <!--按钮-->
    <s:String x:Key="StrSend">发送</s:String>
    <s:String x:Key="StrSave">保存</s:String>
    <s:String x:Key="StrNewSession">新建会话</s:String>

    <!--工具提示-->
    <s:String x:Key="StrAboutOpenGptChat">关于 OpenGptChat</s:String>
    <s:String x:Key="StrGoBackToMainPage">返回到主页面</s:String>
    <s:String x:Key="StrResetChat">重置聊天</s:String>
    <s:String x:Key="StrGoToConfigurationPage">转到配置页面</s:String>

    <!--菜单-->
    <s:String x:Key="StrCopy">复制</s:String>
    <s:String x:Key="StrEdit">编辑</s:String>
    <s:String x:Key="StrDelete">删除</s:String>
</ResourceDictionary>
```

### 2. 编写资源切换的逻辑


按照我们刚刚所说的逻辑, 只需要稍加完善, 即可得到下面用于语言切换的代码:

```cs
public class LanguageService
{
    private static string resourceUriPrefix = "pack://application:,,,/OpenGptChat.Common;component";

    private static Dictionary<CultureInfo, ResourceDictionary> languageResources =
        new Dictionary<CultureInfo, ResourceDictionary>()
        {
            { new CultureInfo("en"), new ResourceDictionary() { Source = new Uri($"{resourceUriPrefix}/Languages/en.xaml" ) } },
            { new CultureInfo("zh-hans"), new ResourceDictionary() { Source = new Uri($"{resourceUriPrefix}/Languages/zh-hans.xaml" ) } },
            { new CultureInfo("zh-hant"), new ResourceDictionary() { Source = new Uri($"{resourceUriPrefix}/Languages/zh-hant.xaml" ) } },
            { new CultureInfo("ja"), new ResourceDictionary() { Source = new Uri($"{resourceUriPrefix}/Languages/ja.xaml" ) } },
            { new CultureInfo("ar"), new ResourceDictionary() { Source = new Uri($"{resourceUriPrefix}/Languages/ar.xaml" ) } },
            { new CultureInfo("es"), new ResourceDictionary() { Source = new Uri($"{resourceUriPrefix}/Languages/es.xaml" ) } },
            { new CultureInfo("fr"), new ResourceDictionary() { Source = new Uri($"{resourceUriPrefix}/Languages/fr.xaml" ) } },
            { new CultureInfo("ru"), new ResourceDictionary() { Source = new Uri($"{resourceUriPrefix}/Languages/ru.xaml" ) } },
            { new CultureInfo("ur"), new ResourceDictionary() { Source = new Uri($"{resourceUriPrefix}/Languages/ur.xaml" ) } },
            { new CultureInfo("tr"), new ResourceDictionary() { Source = new Uri($"{resourceUriPrefix}/Languages/tr.xaml" ) } },
        };

    private static CultureInfo defaultLanguage =
        new CultureInfo("en");

    public LanguageService(
        ConfigurationService configurationService)
    {
        // 如果配置文件里面有指定语言, 则设置语言
        CultureInfo language = CultureInfo.CurrentCulture;
        if (!string.IsNullOrWhiteSpace(configurationService.Configuration.Language))
            language = new CultureInfo(configurationService.Configuration.Language);

        SetLanguage(language);
    }
    
    private CultureInfo currentLanguage =
        defaultLanguage;

    public IEnumerable<CultureInfo> Languages =>
        languageResources.Keys;

    public CultureInfo CurrentLanguage
    {
        get => currentLanguage;
        set
        {
            if (!SetLanguage(value))
                throw new ArgumentException("Unsupport language");
        }
    }
    
    public bool SetLanguage(CultureInfo language)
    {
        // 查找一个合适的 key (先查找完全符合的语言)
        CultureInfo? key = Languages
            .Where(key => key.Equals(language))
            .FirstOrDefault();
            
        // 如果没找到, 就降低要求, 找语言相同的语言
        // 例如资源中只有美式英语, 但是需要切换到英式英语
        // 因为这俩是同一个语言只是地区不同, 所以也能切换过去
        if (key == null)
            key = Languages
                .Where(key => key.TwoLetterISOLanguageName == language.TwoLetterISOLanguageName)
                .FirstOrDefault();

        if (key != null)
        {
            // 取资源字典
            ResourceDictionary? resourceDictionary = languageResources[key];

            // 取旧的资源
            var oldLanguageResources =
                Application.Current.Resources.MergedDictionaries
                    .Where(dict => dict.Contains("IsLanguageResource"))
                    .ToList();
            //删去旧的字典
            foreach (var res in oldLanguageResources)
                Application.Current.Resources.MergedDictionaries.Remove(res);

            // 添加新的资源
            Application.Current.Resources.MergedDictionaries.Add(resourceDictionary);

            currentLanguage = key;
            return true;
        }

        return false;
    }
}
```


### 3. 原理相同的配色切换


配色切换也是一样的逻辑, 也使用一个 Key 来标识当前资源是 "配色" 资源, 切换的时候也是如此, 拿到旧的资源, 删掉它们, 然后添加新的资源.



## 结语:


这些都是我在编写 [OpenGptChat](https://github.com/SlimeNull/OpenGptChat) 时总结出来的用法, 想学习 WPF 建议去看一下这个项目.
