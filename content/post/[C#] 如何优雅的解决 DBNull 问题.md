---
title: '[C#] 如何优雅的解决 DBNull 问题'
slug: '[CSharp]如何优雅的解决DBNull问题'
date: 2021-07-14 23:38:02
tags:
  - 数据库
  - .net
  - csharp
categories:
  - .NET
description: '什么是 DBNull 问题指从数据库取出数据时, 数据为空, 表现为 DBNull 无法转换为其他类型异常示例:// reader 为 DbDataReaderDateTime value = reader.GetDateTime(0);  // 在这里如果数据为空, 则会抛出异常普通的解决方式:DateTime value = reader.IsDBNull(0) ? default(DateTime) : reader.GetDateTime(0);优雅而又牛啤的解决方式:// 新建.'
---

1. 什么是 DBNull 问题
    指从数据库取出数据时, 数据为空, 表现为 DBNull 无法转换为其他类型
2. 异常示例:
    ```csharp
    // reader 为 DbDataReader
    DateTime dt = reader.GetDateTime(0);  // 在这里如果数据为空, 则会抛出异常
    ```
3. 普通的解决方式:
    ```csharp
    DateTime dt = reader.IsDBNull(0) ? default(DateTime) : reader.GetDateTime(0);
    ```
4. 优雅而又牛啤的解决方式:
    ```csharp
    // 新建一个文件, 写入以下内容
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    
    namespace NullLib.DbHelper
    {
        public static class DbExt
        {
            public static T Cast<T>(this object obj)    // 使用 default 默认值
            {
                if (obj is T rst)
                    return rst;
                return default;
            }
            public static T Cast<T>(this object obj, T dft)   // 用户指定默认值
            {
                if (obj is T rst)
                    return rst;
                return dft;
            }
            public static object CastDb(this object obj)  // 将CLR对象转换为数据库类型
            {
                if (obj is null)
                    return DBNull.Value;
                return obj;
            }
        }
    }
    ```
    然后在取值的时候这样做:
    ```csharp
    DateTime dt = reader[0].Cast<DateTime>();  // 如果取值失败则会返回 default(DateTime)
    ```
