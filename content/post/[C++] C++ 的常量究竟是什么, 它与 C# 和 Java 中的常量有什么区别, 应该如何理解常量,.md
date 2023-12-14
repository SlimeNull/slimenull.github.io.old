---
title: '[C++] C++ 的常量究竟是什么? 它与 C# 和 Java 中的常量有什么区别? 应该如何理解常量?'
slug: '[CPP]CPP的常量究竟是什么,它与CSharp和Java中的常量有什么区别,应该如何理解常量,'
date: 2022-11-15 11:06:33
tags:
  - c++
  - 开发语言
categories:
  - C++
description: '更好的认识 C++ 中的 const'
---

在 C++ 中, const 虽然是常量, 然而这个常量比起 C#/Java 中的常量, 似乎又有点不对劲


> 文章面向的人群是拥有 C#/Java 基础的程序员


## 运行时赋值

C++ 的常量不是在编译时就直接确定值的, 你在运行时也可以初始化它, 下面是示例代码:

```cpp
#include <iostream>

int main()
{
    int input;
    std::cin >> input;

    const int unchangable_integer = input;
    std::cout << unchangable_integer << std::endl;
}
```


## 不可直接变更

const 变量一旦初始化之后, 就不再可以直接更改 (直接对其赋值)

```cpp
#include <iostream>

int main()
{
    int input;
    std::cin >> input;

    const int unchangable_integer = input;
    
    unchangable_integer = 114514;           // 这里编译错误
}
```


## 强制更改常量

如果一个常量是在运行时确认值的, 那么可以通过指针强制更改, 并且在二次取值的时候也能够拿到更改后的值.


```cpp
#include <iostream>

int main()
{
    int input;
    std::cout << ":";
    std::cin >> input;                                     // 输入 123

    const int unchangable_integer = input;

    std::cout << unchangable_integer << std::endl;         // 输出 123

    int* changable_integer = (int*)&unchangable_integer;   // 转为指针
    *changable_integer = 114514;                           // 赋值
    
    std::cout << unchangable_integer << std::endl;         // 输出 114514 (值被成功更改了)
    std::cout << *changable_integer << std::endl;          // 输出 114514
}
```


如果一个常量是在编译时就确认值了, 虽然也可以更改, 但是却有些奇怪表现...


```cpp
#include <iostream>

int main()
{
    const int integer = 123;
    int* mutable_integer = (int*)&integer;        // 取可更改的指针

    *mutable_integer = 114514;                    // 改值

    const int* p_integer = &integer;

    std::cout << integer << std::endl;               // 直接输出, 结果是 123
    std::cout << *mutable_integer << std::endl;      // 输出指针目标值, 结果是 114514

    std::cout << *&integer << std::endl;             // 取地址然后输出目标值, 结果是 13
    std::cout << *p_integer << std::endl;            // 取保存为变量的地址的目标值, 结果是 114514
    
    std::cout << &integer << std::endl;              // 取地址, 结果是 0073FDE0
    std::cout << mutable_integer << std::endl;       // 打印之前的指针, 结果是 0073FDE0
}
```


对于上面这些奇怪的表现, 笔者认为, 在编译时直接确认值的常量, 对其的直接引用会被编译器优化为常量, 而不会去取存储了的常量值.


甚至, 如果这个常量压根就没有取地址操作, 那么在程序编译后压根就不会有存储这个变量的, 栈上的内存空间, 它直接就是字面量, 存储在程序 rdata 段(只读数据段), 完全无法变更.


## 我不会对它进行赋值

如果你在一个函数中声明一个参数, 而这个参数是 const, 那在这里, const 表示的是, 你不打算对这个变量进行赋值(更改).


```cpp
#include <iostream>

void print_int(const int number)
{
    printf("%d\n", number);
}

int main()
{
    const int integer = 123;
    int mutable_integer = 123;

    print_int(integer);            // 可以传入只读整数
    print_int(mutable_integer);    // 也可以传入普通整数
}
```


标记了参数为 const 的参数, 就表示函数不会对他进行变更, 这时调用方也能放心的使用它.


所以, 对于我们平常所编写的函数, 如果不会对参数进行变更, 也可以加上 const 修饰符来使函数的功能更加清晰可读.


# 结语

希望这些简短的介绍能够帮助你更好的认识 C++, 毕竟 C#/Java 和 C++ 的差别还是非常非常大的


另外, 如果上述内容有错误, 欢迎在评论区指出
