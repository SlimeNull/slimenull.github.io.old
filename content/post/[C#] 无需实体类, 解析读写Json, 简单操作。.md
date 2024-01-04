---
title: '[C#] 无需实体类, 解析读写Json, 简单操作。'
slug: '20200702232419'
date: 2020-07-02T23:24:19+08:00
tags:
  - csharp
  - json
categories:
  - 类库
  - 算法
description: '1. 关于:这是我自己写的一个类库, 已经封装到了一个dll中, 暂且命名为CHO.Json, 它能够使你像Python那样操作Json数据, 非常适合新手虽然在操作较大的Json数据时, 需要实体类会更方便, 但是这个类库, 可以让你实时判断Json数据的类型. 当然, 以后也会考虑加入需实体类的序列化与反序列化.它具有较大的容差值, 所以允许一些Json不允许的操作, 例如将Bool值作为Object中的键, CHO.Json支持这样, 由CHO.Json生成的Json文本, CHO.Json完'
---

## 1. 关于:

0. 这是我自己写的一个类库, 已经封装到了一个dll中, 暂且命名为CHO.Json, 它能够使你像Python那样操作Json数据, 非常适合新手
1. 虽然在操作较大的Json数据时, 需要实体类会更方便, 但是这个类库, 可以让你实时判断Json数据的类型. 当然, 以后也会考虑加入需实体类的序列化与反序列化.
2. 它具有较大的容差值, 所以允许一些Json不允许的操作, 例如将Bool值作为Object中的键, CHO.Json支持这样, 由CHO.Json生成的Json文本, CHO.Json完全可以读取. 因为其特殊性, 所以可能别的Json读写模块无法识别, 所以, 为了保证正常使用, 即便CHO.Json支持这些操作, 但务必遵守标准Json的规则.


> 最新版的某些函数已经与介绍有些出入, 请查看新文章 [CHO.Json操作](https://blog.csdn.net/m0_46555380/article/details/109348146)
> 已经添加了关于实体类的序列化与反序列化功能, 详见 [Github仓库](https://github.com/SlimeNull/CHO.Json)


## 2. 原理:

0. 由一个JsonData类来表示任何Json数据, 包括Object, Array, String, ==Integer, Float, Double, Boolean==, Null, 其中由枚举属性DataType来表示该JsonData属于何种数据类型.
1. 分析Json文本, 这个就无需多言了, 如果感兴趣, 可以联系我, 获取它的源代码. 当然, 你也可以直接将dll反编译.

## 3. 命名空间:

0. 该Json解析方式所需的成员位于dll中 CHO.Json 命名空间下
1. 命名空间下包含了一些异常类型: JsonDataTypeException, InvalidCharParseException, NotClosedParseException, ParseCallError, ParseUnknownError, JsonFormatParseException这些继承于Exception的类, 它们分别表示了 Json数据类型异常, 分析Json文本时的非法字符异常, 分析Json文本时的数据未结束异常, 分析Json文本时的调用错误, 分析Json文本时的未知错误(该错误在正常使用时不会出现, 除非用户自定义的类继承JsonData, 并写了一些奇怪的代码), 分析Json文本时的Json数据格式错误.
2. 命名空间下包含了枚举类型 JsonDataType, 它的成员有这些: Object, Array, String, Number, Bool, Null. 这些表示了JsonData中所包含的Json数据的类型.
3. 命名空间下包含的JsonData是CHO.Json的核心, 它提供了对所有Json数据的操作方法, 例如Array的Add方法, 提供了分析Json文本的Parse方法, 将JsonData示例转换为Json文本的ToJsonTest方法, 注意, 在使用一种操作Json数据的方法前, 请确保, 实例所包含的Json数据是方法支持的, 否则将抛出JsonDataTypeException异常.

## 4. 使用:

0. CHO.Json的使用基本上是对JsonData的操作, 下面给出一个实例, 以了解CHO.Json的基本使用方法.

1. 这是一个Json文本, 接下来将使用程序来读取它并输出一些值


> {
    "姓名": "小明",
    "性别": "男",
    "年龄": 16,
    "境内": true,
    "自述": null,
    "学习科目": [
         "语文",
         "数学",
         "英语",
         "信息技术"
     ]
}

2. 将其转换为字符串表达式后: ==注意, 在新版本中, 将JsonData实例转换为Json文本你需要使用JsonData的静态方法: ConvertToText==

> "{\n    \"姓名\": \"小明\",\n    \"性别\": \"男\",\n    \"年龄\": 16,\n    \"境内\": true,\n    \"自述\": null,\n    \"学习科目\": [\n         \"语文\",\n         \"数学\",\n         \"英语\",\n         \"信息技术\"\n     ]\n}"


```csharp
using System;
using CHO.Json;

namespace CSharpJson
{
    class Program
    {
        static void Main(string[] args)
        {
            JsonData json = JsonData.Parse("{\n    \"姓名\": \"小明\",\n    \"性别\": \"男\",\n    \"年龄\": 16,\n    \"境内\": true,\n    \"自述\": null,\n    \"学习科目\": [\n         \"语文\",\n         \"数学\",\n         \"英语\",\n         \"信息技术\"\n     ]\n}");
            if (json.DataType == JsonDataType.Object)
            {
                foreach(JsonData key in json.GetKeys())
                {
                    Console.WriteLine(string.Format("{0}: {1}", key.ToJsonText(), json[key].ToJsonText()));
                }
            }
            else
            {
                Console.WriteLine("读取出现了错误");
            }
            Console.ReadKey();
        }
    }
}

```

3. 输出结果是:


> "姓名": "小明"
"性别": "男"
"年龄": 16
"境内": true
"自述": null
"学习科目": ["语文", "数学", "英语", "信息技术"]


4. 从结果来看, 正常分析了Json文本并且成功将Json数据转换为了Json文本.

## 下载:

Github: [Github仓库](https://github.com/SlimeNull/CHO.Json)
.
.
.
.
.

#### 补充:

. . .查看元数据时并不能看到方法注释, 所以, 推荐查看源代码, 那里有完整的注释.
. . .自述 : 自学C#的一只小辣鸡~ (欢迎与我交流~ []~ (￣▽￣)~*)
