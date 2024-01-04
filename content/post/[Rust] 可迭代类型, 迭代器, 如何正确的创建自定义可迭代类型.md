---
title: '[Rust] 可迭代类型, 迭代器, 如何正确的创建自定义可迭代类型'
slug: '[Rust]可迭代类型,迭代器,如何正确的创建自定义可迭代类型'
date: 2023-12-03T20:20:48+08:00
tags:
  - rust
  - 开发语言
categories:
  - Rust
  - note
description: '1. 对于一次性使用的类型, 可以直接对其实现迭代器 trait.2. 对于容器, 不应该对容器本身直接实现迭代器, 而是应该单独创建迭代器类型, 然后对其本身实现 `IntoIterator`'
---

在 Rust 中, for 语句的执行依赖于类型对于 `IntoIterator` 的实现, 如果某类型实现了这个 trait, 那么它就可以直接使用 for 进行循环.


<br/>


## 直接实现


在 Rust 中, 如果一个类型实现了 `Iterator`, 那么它会被同时实现 `IntoIterator`, 具体逻辑是返回自身, 因为自身就是迭代器.


但是如果自身就是迭代器的话, 就意味着自身必须存储迭代状态, 例如当前迭代的位置. 如果是这样的话, 迭代器就只能被使用一次. 况且自身直接被传入 `into_iter` 方法后, 所有权被转移, 该对象就无法被再次使用了.


定义类型本身:


```rust
struct IntRange {
    current: i32,
    step: i32,
    end: i32
}
```


直接为其实现迭代器:


```rust
impl Iterator for IntRange {
    type Item = i32;

    fn next(&mut self) -> Option<Self::Item> {
        if self.current == self.end {
            return None;
        } else {
            let current = self.current;
            self.current += self.step;
            
            return Some(current);
        }
    }
}
```


使用该类型:


```rust
let range = IntRange { current: 0, step: 1, end: 10 };
for value in range {
    println!("v: {}", value);
}
```


所以结论是, 如果你的类型是一次性用品, 你可以直接对其实现 `Iterator`




<br/>




## 手动实现迭代器


如果你向手动实现类似于容器的东西, 那么它当然不是一次性的. 我们应该仿照 Rust 中对切片的迭代器实现.


1. 同时实现会转移所有权和不会转移所有权的两个迭代器
2. 对 `self` 和 `&self` 都实现 `IntoIterator`, 这样就可以做不转移所有权的迭代了


类型本身:


```rust
struct IntRange {
    step: i32,
    end: i32
}
```


两个迭代器:


```rust
struct IntRangeIter<'a> {
    range: &'a IntRange,
    current: i32,
}

struct IntRangeIntoIter {
    range: IntRange,
    current: i32,
}
```


两个迭代器实现:


```rust
impl Iterator for IntRangeIter<'_> {
    type Item = i32;
    
    fn next(&mut self) -> Option<Self::Item> {
        if self.current == self.range.end {
            return None;
        } else {
            let current = self.current;
            self.current += self.range.step;
            return Some(current);
        }
    }
}

impl Iterator for IntRangeIntoIter {
    type Item = i32;
    
    fn next(&mut self) -> Option<Self::Item> {
        if self.current == self.range.end {
            return None;
        } else {
            let current = self.current;
            self.current += self.range.step;
            return Some(current);
        }
    }
}
```


实现返回两种迭代器的 `IntoIterator`:


```rust
impl<'a> IntoIterator for &'a IntRange {
    type Item = i32;
    type IntoIter = IntRangeIter<'a>;

    fn into_iter(self) -> Self::IntoIter {
        IntRangeIter {
            range: self,
            current: 0
        }
    }
}

impl IntoIterator for IntRange {
    type Item = i32;
    type IntoIter = IntRangeIntoIter;
    
    fn into_iter(self) -> Self::IntoIter {
        IntRangeIntoIter {
            range: self,
            current: 0
        }
    }
}
```


使用它:


```rust
let range = IntRange { step: 1, end: 10 };

// 可以使用引用来进行 for 循环
for value in &range {
    println!("v: {}", value);
}

// 也可以直接对其进行 for 循环
for value in range {
    println!("v: {}", value);
}
```




<br/>


## 切片对迭代的实现


我们知道, Rust 的切片有一个 `iter` 方法, 其实它就相当于对当前切片的引用调用 `into_iter`.


其实, 在调用切片引用的 `into_iter` 方法时, 本质上就是调用的其 `iter` 方法. 方法的实现是在 `iter` 内的.


```rust
let v = vec![1, 2, 3];

// 下面两个调用是等价的
let iter1 = v.iter();
let iter2 = (&v).into_iter();
```


如果你希望实现迭代变量可变的迭代器, 还可以为 `&mut T` 实现 `into_iter`, 当然, Rust 内部对于切片的实现, 也是这样的:


```rust
let mut v = vec![1, 2, 3];

// 下面两个调用是等价的
let mutIter = v.iter_mut();
let mutIter = (&mut v).into_iter();
```




<br/>


## 总结


两种类型:


1. 对于一次性使用的类型, 可以直接对其实现迭代器 trait.

2. 对于容器, 不应该对容器本身直接实现迭代器, 而是应该单独创建迭代器类型, 然后对其本身实现 `IntoIterator`


为了方便用户使用, 调用之间的实现应该是这样:


1. 实现 `T` 的 `IntoIterator`
2. 实现 `&T` 的 `iter` 函数, 返回借用的迭代器.
3. 实训 `&mut T` 的 `iter_mut` 函数, 返回可变借用的迭代器.
4. 对 `&T` 和 `&mut T` 实现 `into_iter` 函数, 并在内部调用刚刚实现的 `iter` 和 `iter_mut` 函数.


这样, 用户就可以直接调用 `iter` 方法获得借用的迭代器, 然后使用 `map`, `filter` 等方法进行集合的复杂操作了
