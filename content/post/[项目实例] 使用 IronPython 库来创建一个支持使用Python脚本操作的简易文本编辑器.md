---
title: '[项目实例] 使用 IronPython 库来创建一个支持使用Python脚本操作的简易文本编辑器'
slug: '20210203064309'
date: 2021-02-03T06:43:09+08:00
tags:
  - csharp
  - dotnet
  - python
categories:
  - 桌面程序
  - dotnet
  - 成长记录
description: '步骤 :打开 nuget 包管理器, 工具 -> NuGet 包管理器 -> 管理解决方案的 NuGet 程序包.在 nuget 包管理器中找到 IronPython, 安装到你的项目.using 所需的命名空间, Microsoft.Scripting, Microsoft.Scripting.Hosting, Microsoft.Win32, IronPython.Hosting.创建 Python 引擎:ScriptEngine engine = Python'
---

## 步骤 :

1. 打开 nuget 包管理器, 工具 -> NuGet 包管理器 -> 管理解决方案的 NuGet 程序包.

![nuget包管理器](images/20210203030753805.png)

2. 在 nuget 包管理器中找到 IronPython, 安装到你的项目.

![nuget中的IronPython](images/20210203030547151.png)
3. using 所需的命名空间, Microsoft.Scripting, Microsoft.Scripting.Hosting, Microsoft.Win32, IronPython.Hosting.
4. 创建 Python 引擎:
	```csharp
	ScriptEngine engine = Python.CreateEngine();
	```
5. 创建定义域(Scope), 它用来存储变量:
	```csharp
	ScriptScope scope = engine.CreateScope();
	```
6. 在 Scope 中设置与获取变量值:
	```csharp
	scope.SetVariable("name", "value");   // 其中, name是变量名, "value"可以是任意类型, 表示变量值
	```
	获取变量也差不多, 是GetVariable.
7. 设置引擎的标准输出流以捕获内容:
	```csharp
	TriggerStream stream = new TriggerStream();  // TriggerStream是一个能够在写入时触发事件的, 继承了Stream的类.
	engine.Runtime.IO.SetOutput(stream, Encoding.Default);  // 这样, 我们可以通过TriggerStream的写入事件来获取写入的内容
	```
	提示: 了解 TriggerStream, 请查看这篇文章: [支持事件的Stream](https://blog.csdn.net/m0_46555380/article/details/113578296), 关于为什么使用 Encoding.Default 而不使用 UTF-8, 是因为在Windows里面, 都是用的 ANSI. 而Default就是获取ANSI的编码(在中国是GBK)
8. 执行 Python 代码:
	```csharp
	ScriptSource thisSrc = engine.CreateScriptSourceFromString("print('hello world')", SourceCodeKind.File)
	thisSrc.Execute(scope);   // 代码在这个定义域种执行
	```
	SourceCodeKind.File指这个字符串是来自文件的代码, 也就是说你可以在里面加换行加缩进以定义一个语句块之类的. 还有一个就是SourceCodeKind.Expression, 指一个表达式.


## 项目 :

项目已经在 GitHub 开源, 地址: [SlimeNull/Null.TextEditor](https://github.com/SlimeNull/Null.TextEditor)
想直接下载成品? 希望你能下载成功: [Release/Null.TextEditor.exe](https://github.com/SlimeNull/Null.TextEditor/raw/main/TextEditor/bin/Release/Null.TextEditor.exe)
