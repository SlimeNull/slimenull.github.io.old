---
title: '[C#] C# 与 Java 的一些差异'
date: 2021-04-18 11:07:46
tags:
  - c#
  - java
  - 编程语言
categories:
  - 灌水
  - 笔记
  - 成长记录
description: '这篇文章, 可以帮助你借助 C# 的知识快速入门 Java, 并且详细介绍 C# 与 Java 的重要差异1. 数据类型在 C# 中, 基本的数据类型都有别名, 例如字符串 String, 可以简写为 string, Int32 可以简写为 int, 但是在 Java 中, 不存在这些. 使用字符串, 必须要首字母大写, 使用布尔值必须要用 boolean.在 C# 中, String 数据基本数据类型, 而在 Java 中, 严格来讲, 它不属于基本数据类型. Java 中的基本数据类型更像是.'
---

> 这篇文章, 可以帮助你借助 C# 的知识快速入门 Java, 并且详细介绍 C# 与 Java 的重要差异


## 1. 数据类型

1. 在 C# 中, 基本的数据类型都有别名, 例如字符串 String, 可以简写为 string, Int32 可以简写为 int, 但是在 Java 中, 不存在这些. 使用字符串, 必须要首字母大写, 使用布尔值必须要用 boolean.
2. 在 C# 中, String 数据基本数据类型, 而在 Java 中, 严格来讲, 它不属于基本数据类型. Java 中的基本数据类型更像是 C# 中的值类型, 只有 int, char, boolean 这些直接存储的数据类型才是基本数据类型.
    ```java
    String str = "这是一个字符串";          // 正确
    ```
4. 在 C# 中, int 就是 int, 它与其他复杂的数据类型例如 StringBuilder 差异并不大, 但是在 Java 中, int, char, boolean 这类基本数据类型不可以作为泛型参数来使用, 例如你不能创建一个存储 int 的 List, 如果要创建存储 int 的 List, 你需要使用 int 的包装类 Integer, 创建 ArrayList&lt;Integer&gt;. 
    ```java
     var v = new ArrayList<int>();        // 错误
     var v = new ArrayList<Integer>();    // 正确
     ```
5. Java 不存在值类型, 只有基本数据类型和引用对象类型, 两者用法不同.
6. Java 的基本数据类型不存在成员, 例如在 C# 中, int 有静态常量 MaxValue, 但是 Java 中的 int 没有, 如果要获取整数的最大值, 需要使用 int 的包装类 Integer
    ```java
    int v = int.MAX_VALUE;      // 错误
    int v = Integer.MAX_VALUE;  // 正确
    ```

## 2. 枚举

1. 在 C# 中, 枚举是特殊的值类型, 本质是 int, 定义起来非常简单, 在 Java 中, 如果要自定义每一个枚举成员所对应的 int 值, 则定义起来非常繁琐:
    ```java
    public enum ColumnAlignment {
	    Left(1), Right(2);

    	private int value;
	    private ColumnAlignment(int value) {
        	this.value = value;
    	}
    	public int value(){
        	return this.value;
    	}
	}
    ```

## 3. Linq 与 Stream

1. 在 C# 中, 对于可迭代对象的批量处理, 我们经常使用 Linq, 但是在 Java 中, 是不支持拓展函数的, 所以自然无法实现 Linq 这类方便的操作类, Stream 勉强可以作为 Linq 的替代品, 例如与 Linq 中 Select 方法对应的 Stream.map:
    ```csharp
    // C#:
    List<string> strs = new int[]{1,2,3,4,5,6,7,8,9,0}.Select(v => v.ToString()).ToList();
    ```
    ```java
    // Java:
    List<String> strs = Arrays.stream(new int[]{1, 2, 3, 4, 5, 6, 7, 8, 9, 0}).map(v -> v.toString()).collect(Collectors.toList());
    ```

不过在实际使用中, 由于 Java 区分 int 和 Integer, 所以会有诸多不便, 但大多都有解决方案, 所以 Java 只是会让人感到有些抓狂罢了.


## 4. Lambda:

匿名函数, 这绝对是最舒服的东西之一了, 在使用 Linq 的时候肯定无时无刻使用 lambda 的.

1. 在 C# 中, lambda 表达式的操作符是: `=>`, 而 Java 中是 `->`
2. 在 C# 中, lambda 可以使用委托来存储
    ```csharp
    Action action = () =>
    {
        Console.WriteLine("F**k you, world");
    };
    ```
    在 Java 中, 需要使用接口来存储委托
    ```java
    interface Action{
        void execute();    // 方法名随意
    }

	Action action = () -> {
	    System.out.println("F**k you, world");
	}
    ```

## 5. 泛型

这绝对是 Java 中最让人不爽的地方了.

1. 你无法创建泛型数组
    ```java
    static <T> T[] createArray(int len){
        return new T[len];                    // 会报错, '类型参数 T 无法直接实例化'
    }
    ```
2. 你无法判断一个对象是否是某个泛型类型
	```java
	static <T> boolean isType(Object obj){
	    return obj instanceof T;              // 会报错
	    // 同样, T.class 也是不可用的
	}
	```
3. 你无法在使用如下重载:
    ```java
    public class Test<T> {
        void Action(List<T> list){};
        void Action(List<Integer> list){};    // 会报错:两个方法具有同样的擦除
    }
    ```
