---
title: '在 System.Text.Json 中使用构造函数进行反序列化'
slug: '在System.Text.Json中使用构造函数进行反序列化'
date: 2022-09-14 13:11:37
tags:
  - csharp
  - json
  - dotnet
categories:
  - 灌水
description: '使用 System.Text.Json 时, 不可变类型的反序列化'
---

# 在 System.Text.Json 中使用构造函数进行反序列化


有的时候, 我们希望一些类型在实例化之后, 就无法更改, 也就是说, 属性只读, 但是如果这样, System.Text.Json 就无法对属性进行赋值, 可以这样:


```csharp
class UserModel
{
    public static string Username { get; set; }
    public static string Password { get; set; }
}
```


改为:


```csharp
class UserModel
{
    public UserModel(string username, string password)
    {
        Username = username;
        Password = password;
    }

    public static string Username { get; }
    public static string Password { get; }
}
```


这里, 我们暴露了一个公共的构造函数, 传入 username 与 password, 并对只读属性进行初始化. 这里, 构造函数的参数名和属性名必须是一一对应的, 允许大小写不同.


需要注意的是, 如果你使用 JsonPropertyName 对 JSON 属性进行重命名, 你也必须保证构造函数参数名和属性名一致, 否则 System.Text.Json 将无法进行反序列化. 示例:


```csharp
class UserModel
{
    public UserModel(string username, string password)
    {
        Username = username;
        Password = password;
    }

    [JsonPropertyName("user")]
    public static string Username { get; }

    [JsonPropertyName("pwd")]
    public static string Password { get; }    
}
```


另外, 如果你的属性类型和参数类型不一致, 例如构造函数要求传入 string, 而属性是 Uri 的时候, 也是不允许的, System.Text.Json 无法识别它. 示例


```csharp
class UserModel
{
    public UserModel(string username, string password, string address)
    {
        Username = username;
        Password = password;
        Address = new Uri(address);
    }

    [JsonPropertyName("user")]
    public static string Username { get; }

    [JsonPropertyName("pwd")]
    public static string Password { get; }
	
	// 这里与构造函数不同, 此类型无法正常反序列化
    [JsonPropertyName("addr")]
    public static Uri Address { get; }
}
```

