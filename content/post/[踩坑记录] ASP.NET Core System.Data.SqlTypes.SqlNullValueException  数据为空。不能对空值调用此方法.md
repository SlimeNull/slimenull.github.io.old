---
title: '[踩坑记录] ASP.NET Core System.Data.SqlTypes.SqlNullValueException: 数据为空。不能对空值调用此方法'
slug: '20210129050708'
date: 2021-01-29T05:07:08+08:00
tags:
  - mysql
  - sql
  - csharp
  - asp.net
categories:
  - 成长记录
  - note
  - dotnet
description: '问题是出在这里的:SoftwareInfo result = new SoftwareInfo(reader.GetString(1),              // reader 是 MySqlDataReader 实例reader.GetString(7)){    ID = reader.GetInt32(0),    Label = reader.GetString(2),    Coder = reader.GetString(3),    DownloadCount = rea'
---

## 问题是出在这里的:

```csharp
SoftwareInfo result = new SoftwareInfo(
reader.GetString(1),              // reader 是 MySqlDataReader 实例
reader.GetString(7))
{
    ID = reader.GetInt32(0),
    Label = reader.GetString(2),
    Coder = reader.GetString(3),
    DownloadCount = reader.GetInt32(4),
    Stars = reader.GetFloat(5),
    Introduction = reader.GetString(6)
};

return result;
```

## 解决方案:

**第一种:** 添加 Null 检查, 在调用 GetString, GetInt32, GetFloat 这类包含具体的类型转换的函数前, 先调用 IsDBNull 检查这条数据是不是 Null.

```csharp
SoftwareInfo currentInfo = new SoftwareInfo(
reader.GetString(1),
reader.GetString(7))
{
    ID = reader.GetInt32(0),
    Label = reader.IsDBNull(2) ? null : reader.GetString(2),
    Coder = reader.IsDBNull(3) ? null : reader.GetString(3),
    DownloadCount = reader.IsDBNull(4) ? 0 : reader.GetInt32(4),
    Stars = reader.IsDBNull(5) ? 0 : reader.IsDBNull(5) ? 0 : reader.GetFloat(5),
    Introduction = reader.IsDBNull(6) ? null : reader.GetString(6)
};

return currentInfo;

```

**第二种:** 直接指定数据库的字段不可为 Null, 从根本上杜绝空值

```sql
alter table tablename modify columnname not null;
```

## 原因:

如果数据是 Null, 就无法调用包含具体类型的方法, 因为它们内部涉及到类型转换.
