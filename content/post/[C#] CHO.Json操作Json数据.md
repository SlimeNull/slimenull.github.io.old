---
title: '[C#] CHO.Json操作Json数据'
slug: '[CSharp]CHO.Json操作Json数据'
date: 2020-10-28 23:52:44
tags:
  - csharp
  - json
  - .net
categories:
  - 算法
  - .NET
  - 类库
description: '这是一个类似于Newtonsoft.Json的项目, 但与其有些出入。这是它与Newtonsoft.Json的差别:CHO.Json支持你像Python那样不需要实体类而简便的操作小型数据, 也支持将类的实例序列化为Json文本与将分析完毕的Json数据反序列化为特定类的实例CHO.Json少了许多冗余的功能, 例如将图片序列化为字符串, 因此CHO.Json可能要比Newtonsoft.Json轻量许多。CHO.Json的源代码比Newtonsoft.Json更适合初学者阅读, 在看懂它的代.'
---


## CHO.Json：

1. 现在，CHO.Json已经成为了一个强大的Json解析库，速度很快。
2. 支持弱类型操作, JsonData实现了所有Json数据类型的基本操作, 支持像 Python 那样的操作方式
3. 支持强类型操作, JsonData可以直接通过 ‘Get类型’ 方法来获取对应数据类型数据并进行操作
4. 支持序列化， 反序列化， 一次分析多个Json数据：
    1. 序列化和反序列化则是所有库都有的功能
    2. 一次性序列化多个Json数据则是，允许一段文本中含有多个Json根数据，它可以用于解决TCP套接字的粘包问题
5. CHO.Json 拥有教快的分析速度，能够完全与Json.Net与Text.Json匹敌，并且它的加载速度时三者中最快的。
![实验数据：CHO.Json的速度优势](images/20201231000802877.png)




**这是它与Newtonsoft.Json的差别:**


- CHO.Json少了许多冗余的功能, 例如将图片序列化为字符串, 因此CHO.Json可能要比Newtonsoft.Json轻量许多。
- CHO.Json的源代码比Newtonsoft.Json更适合初学者阅读, 在看懂它的代码后, 你会了解到有限状态机以及反射
- CHO.Json仅使用一个C#源文件, 这是因为它的源代码仅有1.5k行左右, 这其中还包含类型转换等片段


**更详细的介绍：**

1. 首先， 在 CHO.Json 中, 包含一个 JsonData 类, 它可以用来表示任何 Json 数据, 通过 JsonData 类的静态方法, 可以从字符串或字符数组中分析出 Json 数据并存储到 JsonData 实例中, 或者将 JsonData 实例转化为 Json 字符串.
2. JsonData 是你最常用的类, 有一个 DataType 属性指定了这个实例所表示的 Json 数据类型, 例如String, Boolean, Integer, 通过 'Get类型' 方法可以直接获取对应数据, 例如: GetString() 方法返回这个Json实例中所包含的字符串信息. 但如果你对一个包含了非字符串信息的 JsonData 实例使用这个方法, 则会抛出异常.
3. 在最新版本的 CHO.Json 中, Serializie(序列化)和Deserialize(反序列化)用于直接转换字符串和指定的数据. 但在旧版, 则是转换 JsonData. 新版中, 推荐的方法是使用 JsonData 的静态方法: Parse, Create, ConvertToInstance, ConvertToText.
4. 如果一个字符串里包含多个Json数据, 但并没有分隔符, 例如在TCP套接字中传输的多个Json文本, 你可以通过JsonData的静态方法 ParseStream 来分析它们.
5. JsonData 的 Content 属性是 JsonData 包含的数据原型, 如果是Array, 则它的类型是List&lt;JsonData&gt;, 如果是Object, 则它的类型是 Dictionary&lt;JsonData, JsonData&gt;

   ![在这里插入图片描述](images/20201231002914420.png)


## 下面是使用CHO.Json的例子:

![使用套接字发送经过CHO.Json转换过的信息](images/20201231002450466.png)



项目完整源代码: [Github仓库](https://github.com/SlimeNull/EleCho.Json)
