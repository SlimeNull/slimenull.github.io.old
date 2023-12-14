---
title: '[C++] 栈上存储以及堆中存储, 值引用, 类型拷贝以及拷贝构造函数, 将类分配到堆中'
slug: '[CPP]栈上存储以及堆中存储,值引用,类型拷贝以及拷贝构造函数,将类分配到堆中'
date: 2022-11-15 12:50:40
tags:
  - c++
  - 开发语言
categories:
  - C++
description: '[C++] 栈上存储以及堆中存储, 值引用, 类型拷贝以及拷贝构造函数, 将类分配到堆中'
---

C++ 中的 class, 简直和 C#/Java 中的完全不一样了, 因为 C++ 的 class... 是分配在栈上的.


## 赋值时的拷贝行为

一个单纯的 class, 其实也和 int 一样, 在被赋值时, 会被拷贝, 你可以理解为, 他和结构体其实没啥区别, 只是 class 能够声明方法罢了.

```cpp
#include <iostream>

class something
{
    public:
    int integer;
};

int main()
{
    something sth1 = something();                // 新建一个实例
    sth1.integer = 123;                          // 改 integer 的值

    something sth2 = sth1;                       // sth2 将是一个新的实例, 是 sth1 的拷贝
    sth2.integer = 114514;                       // 改 integer 的值

    std::cout << sth1.integer << std::endl;      // 输出 123
    std::cout << sth2.integer << std::endl;      // 输出 114514
}
```


## 标准库中的类都会尝试拷贝所有内容

当你赋值一个 std::vector 的时候, 它也会拷贝自身, 然而它是包含非栈上的内容的(用动态内存存储值), 尽管如此, 它也会拷贝所有的东西, 包括容器中的元素.

```cpp
#include <iostream>
#include <vector>

int main()
{
    std::vector<int> vec1 = std::vector<int>{ 1, 2, 3 };
    std::vector<int> vec2 = vec1;                         // vec2 是 vec1 的拷贝

    vec2.push_back(114514);

    std::cout << vec1.size() << std::endl;                // 输出 3
    std::cout << vec2.size() << std::endl;                // 输出 4
}
```



## 通过 & 来使用引用

如果你不希望在变量赋值时进行拷贝, 可以使用 & 对变量进行修饰, 基本的数据类型, 诸如 int, char 是可以的, 同样, class 和 struct 也一样


> 可以通过指针的概念来帮助理解 "引用"


```cpp
#include <iostream>

int main()
{
    int integer = 123;
    int& ref_integer = integer;

    ref_integer = 114514;

    std::cout << integer << std::endl;         // 输出 114514
    std::cout << ref_integer << std::endl;     // 输出 114514
}
```


## 类默认只会进行浅拷贝

例如我们自定义一个类, 类中声明一个指针, 在类进行拷贝时, 指针会被拷贝, 然而新的指针还是指向了原来的值.


```cpp
#include <iostream>
#include <vector>

class something
{
    public:
    int* p_integer;     // 一个指针成员
};

int main()
{
    int integer = 123;                           // 先定义一个整数

    something sth1 = something();                // 定义一个 sth1
    sth1.p_integer = &integer;                   // 改它的 p_integer

    something sth2 = sth1;                       // 获取它的拷贝
    *sth2.p_integer = 114514;                    // 对 sth2 的指针目标值进行更改

    std::cout << *sth1.p_integer << std::endl;     // 输出 114514
    std::cout << *sth2.p_integer << std::endl;     // 输出 114514
}
```


## 通过自己声明拷贝构造函数来实现深拷贝

如果要实现对某些指针成员的深拷贝, 就需要自己定义一个拷贝构造函数了, 只需要声明一个参数为相同类型引用的构造函数即可. (你也可以添加 const 修饰)


```cpp
#include <iostream>
#include <vector>

class something
{
    public:
    int* p_integer;
    something();                             // 无参构造函数
    something(const something& origin);      // 拷贝构造函数 (传入相同类型的引用
};

int main()
{
    int integer = 123;

    something sth1 = something();
    sth1.p_integer = &integer;

    something sth2 = sth1;
    *sth2.p_integer = 114514;

    std::cout << *sth1.p_integer << std::endl;      // 输出 123
    std::cout << *sth2.p_integer << std::endl;      // 输出 114514
}

something::something()
{
    p_integer = NULL;
}

something::something(const something& origin)      // 拷贝构造
{
    p_integer = NULL;
    if (origin.p_integer != NULL)
    {
        p_integer = (int*)malloc(sizeof(int));     // 在堆中分配一个 int

        if (p_integer != NULL)                     // 如果内存分配成功
        {
            *p_integer = *origin.p_integer;        // 把指针目标值拷贝了
        }
    }
}
```


## 类中包含另一个类成员, 成员也会被正确拷贝

例如我们定义了一个类, 然后这个类中用了 std::vector 成员, 在拷贝时, 这个成员也会被 C++ 默认的拷贝构造来进行拷贝


```cpp
#include <iostream>
#include <vector>

class something
{
    public:
    std::vector<int> vec;
};

int main()
{
    something sth1 = something();
    sth1.vec = std::vector<int>{ 1, 2, 3 };      // 设置 sth1 的 vec 为 3 个元素

    something sth2 = sth1;                       // 拷贝一份 sth1
    sth2.vec.push_back(114514);                  // 往 sth2 的 vec 中添加一个新元素

    std::cout << sth1.vec.size() << std::endl;   // 输出 3
    std::cout << sth2.vec.size() << std::endl;   // 输出 4
}
```


## 要千万小心野指针问题

因为类的实例是直接分配在栈上的, 所以千万不能直接返回它的指针, 否则,,, 就野指针了


```cpp
#include <iostream>
#include <vector>

std::vector<int>* new_vector()
{
    std::vector<int> vec = std::vector<int>{ 1, 2, 3 };    // 这个 vector 会在栈上
    return &vec;                                           // 返回它的指针
}

int main()
{
    std::vector<int>* p_vec = new_vector();                // 你虽然拿到了指针, 但是栈已经弹出了

    std::cout << p_vec->size() << std::endl;               // 输出 0 (也有可能是其他的值
}                                                          // 总之不会输出正确的值
```


## 通过 new 来将类分配到堆中

为了避免上面的野指针问题, 我们需要把类型实例存储到堆中, 而不是栈中


```cpp
#include <iostream>
#include <vector>

std::vector<int>* new_vector()
{
    std::vector<int>* vec = new std::vector<int>{ 1, 2, 3 };   // 使用 new 将实例存储到堆中
    return vec;                                                // 返回它
}

int main()
{
    std::vector<int>* p_vec = new_vector();

    std::cout << p_vec->size() << std::endl;            // 输出 3
}
```


当然, 你也可以配合 "引用" 来食用


```cpp
#include <iostream>
#include <vector>

std::vector<int>& new_vector()
{
    std::vector<int>* vec = new std::vector<int>{ 1, 2, 3 };
    return *vec;
}

int main()
{
    std::vector<int>& p_vec = new_vector();            // 注意, 这里必须写 std::vector<int>&

    std::cout << p_vec.size() << std::endl;            // 输出 3
}
```
