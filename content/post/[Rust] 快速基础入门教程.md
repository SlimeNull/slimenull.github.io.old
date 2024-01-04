---
title: '[Rust] 快速基础入门教程'
slug: '[Rust]快速基础入门教程'
date: 2023-11-30T14:19:12+08:00
tags:
  - rust
  - 开发语言
categories:
  - Rust
  - note
description: 'Rust 是一个无运行时的强类型语言, 包含很多高级特性, 例如泛型, lambda 等. 又因为其独有的所有权机制, 所以 Rust 的内存安全要比 C++ 完善许多.'
---

Rust 是一个无运行时的强类型语言, 包含很多高级特性, 例如泛型, lambda 等. 又因为其独有的所有权机制, 所以 Rust 的内存安全要比 C++ 完善许多.




<br/>


## 风格


Rust 与 C 族语言不一样, C 族语言在定义方法, 变量时, 都是 `类型 关键字` 这样的格式, 也就是类型前置. Rust 采用的是类型后置的风格, 即 `关键字: 类型`




<br/>


## 基本结构


Rust 的结构与 C++ 是差不多的, 一个文件的顶部写要引入的内容, 下面是结构, 函数, 特征的声明.


```rust
use std::io::stdout;
use std::io::Write;

fn main() {
    let hello_str = "Hello world";
    let bytes = hello_str.as_bytes();

	stdout().write_all(bytes).unwrap();
}
```


在上面代码中, `std::io::stdout` 与 `std::io::Write` 都是在 `std::io` 下, 它们可以通过花括号合并为以下语句:


```rust
use std::io::{Write, stdout};
```




<br/>


## 基本类型


数字类型


| 长度 | 有符号 | 无符号 |
| --- | --- | --- |
| 8 位 | i8 | u8 |
| 16 位 | i16 | u16 |
| 32 位 | i32 | u32 |
|64 位 | i64 | u64 |
|128 位 | i128 | u128 |
| 平台大小 | isize | usize |


以及三十二位浮点数 `f32` 与六十四位浮点数 `f64`


布尔(逻辑)值 `bool`, 字符(Unicode)值 `char`.


> Rust 中, 一个字符占四个字节, 可以表达任何 Unicode 字符, 包含 Emoji 表情.




<br/>


## 函数声明


使用 `fn` 关键字来声明一个函数.


```rust
fn test1() {
	println!("hello");
}
```


如果要带参数, 直接按照类型后置风格在括号内写明.


```rust
fn test2(number: i32) {
	println!("number: {}", number);
}
```


如果要带返回值, 直接使用箭头 `->` 来指定.


```rust
fn test3(num1: i32, num2: i32) -> i32 {
	return num1 + num2;
}
```


当返回值是最后一行的时候, 你可以省略 return 和结尾的分号, 直接将表达式作为返回值返回.


```rust
fn test3(num1: i32, num2: i32) -> i32 {
	num1 + num2
}
```


如果参数或者返回值是函数类型, 使用 `fn` 关键字即可, 下面的例子中, resolver 是一个函数, 这个函数有两个 `i32` 参数, 返回值是 `i32`


```rust
fn test4(num1: i32, num2: i32, resolver: fn(i32, i32) -> i32) {
	println!("{}", resolver(num1, num2));
}
```




<br/>


## 变量声明


使用 `let` 声明一个变量.


```rust
let num: i32 = 114514;
```


大部分情况, 你都可以省略掉类型标记, Rust 会自动推导它的类型.


```rust
let num = 114514;
```


上面声明的变量, 是不可变的. 如果你希望声明可变的变量, 需要使用 `mut` 关键字.


```rust
let mut num = 114514;

// 更改其值
num = 666;
```




<br/>


## 流程控制


Rust 中的 `if` 不使用括号, 直接跟表达式以及语句即可.


```rust
let num = 114514;
if num == 114514 {
	println!("value is 114514");
}
```


同样, Rust 中也有 `else`, `else if` 可用, 和 C 族语言类似, 只不过是少了括号.


```rust
if num == 114514 {
	println!("value is 114514");
} else if num == 1919810 {
	println!("value is 1919810");
} else {
	println!("invalid value");
}
```


Rust 中的 `if` 也可以实现根据条件返回特定值的需求.


```rust
let num = 114514;
let tip = if num == 114514 {
	"哼哼哼"
} else {
	"啊啊啊"
};
```


在上面的例子中, 对 `num` 进行判断, 如果值为 114514, tip 的值会是 "哼哼哼", 否则为 "啊啊啊". 需要注意的是, 当你希望 `if` 语句将语句作为结果返回时, 不要在语句末尾添加分号.


Rust 中的  `for` 用来对一个实例进行迭代. 使用 `起始值..结束值` 这样的语法可以创建简单的数值范围. 搭配 `for` 即可实现简单的数值循环.


```rust
for i in 0..10 {
	println!("current value: {}", i);
}
```


同样的, Rust 中, `continue` 和 `break` 也可用. 


```rust
for i in 0..10 {
    println!("current value: {}", i);

    if i == 3 {
    	continue;
    }

    if i == 7 {
    	break;
    }
}
```


值得一提的是, Rust 在循环时, 是允许对集合元素进行修改的. 只需要将迭代变量使用 `mut` 修饰.


```rust
let arr = [1, 2, 3, 4];
for mut ele in arr {
	ele = ele * 2;
}
```


如果你需要一个 '死循环', 可以直接使用 `loop` 语句.


```rust
let mut i = 0;
loop {
    println!("current value: {}", i);

    if i == 3 {
    	continue;
    }

    if i == 10 {
    	break;
    }
}
```


Rust 的 `loop` 还支持给循环语句加上标签, 然后在内部循环中直接中断指定标签的循环.


```rust
'loop_out: loop {
    loop {
        // 在内部循环直接中断最外部循环
        break 'loop_out;
    }
}
```


Rust 的 `loop` 还可以作为一个带返回值的表达式使用. 只需要在 break 的时候提供返回值即可.


```rust
let mut value = 0;
let result = loop {
    value += 1;

    if value == 10 {
    	break value * 2;
    }
};
```


Rust 的 `match` 语句可以近似理解为 `if` 的高级语法. 传入一个值, 以及匹配条件和语句, 可以执行对应语句.


```rust
let num = 114514;
match num {
    114514 => println!("hello"),
    1919810 => println!("world"),
    _ => { }
}
```


在上面的例子中, 会对 num 进行匹配, 并且在值为 114514 和 1919810 时执行不同的语句, 如果所有条件都没有匹配到, 则会使用 `_ => { }` 表示的默认情况, 在这里是空语句, 也就是什么也不执行.


同时, Rust 的 `match` 语句也可以作为表达式返回一个值, 只需要 `match` 内的语句是有返回值的表达式即可.


```rust
let result = match num {
    114514 => "hello",
    1919810 => "world",
    _ => ""
};
```


在上面的示例中, `match` 对 num 进行匹配, 并且在值为 114514 和 1919810 的时候返回不同的字符串, 最终赋值给 result. 如果没有匹配到指定条件, 则是使用默认语句 `_ => ""` 返回一个空的字符串.


> 需要注意的是, 在 `match` 语句中, 使用的是 `=>` 而不是 `->`.




<br/>


## 字符串 / Strings


在 Rust 中, 字符串分两种, 一种是 `str`, 它表示字符串本身, 不可变.
由于 `str` 作为字符串本身, 其大小是不确定的, 所以它无法作为本地变量存储. 我们在使用时, 使用的都是 `&str`, 也就是 `str` 的引用. 


```rust
let hello1 : &str = "你好世界";
```


另一种是 `String`, 本质是数组的包装, 它是可变的. 你可以对其进行更改. 你可以将它理解为其他语言中常见的 `StringBuilder`


```rust
let hello2 : String = String::new();

hello2.add("向字符串中添加一些内容");
hello2.add(", 你好吗?")
```


如果你需要将字符串编码为字节数组, 可以直接使用 `as_bytes` 函数


由于 Rust 中字符串使用 UTF-8 存储, 所以该函数的结果即为字符串使用 UTF-8 编码后的结果.


```rust
let tip = "hello world";
let bytes = tip.as_bytes();
```


如果希望从 UTF-8 转为 Rust 字符串, 可以使用 `std::str::from_utf8` 函数进行转换.


```rust
// bytes 为需要解码的数据
let bytes : &[u8];
let some_str = std::str::from_utf8(bytes);
```




<br/>


## 数组 / Arrays


在 Rust 中, 数组时长度不可变的容器, 并且其大小必须在编译时确定.  其类型表达为: `[类型; 长度]`.


```rust
let arr : [i32; 4];
```


在使用这个数组之前, 我们还需要对其进行初始化, 可以使用中括号指定其每一个元素的值.


```rust
let arr : [i32; 4] = [1, 2, 3, 4];
```


当然, 这里的数组类型也可以被省略掉.


```rust
let arr = [1, 2, 3, 4];
```


如果你希望直接初始化一个指定长度的数组, 可以使用中括号以及分号. 就像数组的类型表示.


```rust
let arr : [i32; 4] = [0; 4];
let arr = [0; 4];
```




<br/>


## 容器 / Collections


如果你需要可变的容器, 可以使用 `Vec<T>`, 当然, 只有在声明时使用 `mut` 关键字, 它才可变.


```rust
let mut v : Vec<i32> = Vec::<i32>::new();
```


你可以将它简写为这样:


```rust
// 指定变量类型, Vec 的泛型参数会自动推导
let mut v : Vec<i32> = Vec::new();

// 指定泛型参数, 变量的类型会自动推导.
let mut v = Vec::<i32>::new();
```


使用 `len` 函数获取其长度:


```rust
let len = v.len();
```


用 `push` 和 `pop` 方法可以在 `Vec<T>` 的结尾增删元素.


```rust
v.push(114514);
v.pop();
```


使用 `insert` 和 `remove` 可以在指定位置增删元素.


```rust
v.insert(0, 114514);
v.remove(0);
```


哈希映射(HashMap)用于存储基于哈希值的键值映射, 像是其他语言中的 "Dictionary" 或者 "Hashtable",


```rust
let mut hm : HashMap<&str, &str> = HashMap::<&str, &str>::new();
```


简写:


```rust
let mut hm : HashMap<&str, &str> = HashMap::new();
let mut hm = HashMap::<&str, &str>::new();
```


使用 `len` 函数获取其长度:


```rust
let len = hm.len();
```


insert() 方法用于插入或更新一个键值对到哈希映射中, 如果键已经存在, 则更新为新的键值对, 并则返回旧的值. 如果键不存在则执行插入操作并返回 None.


```rust
hm.insert("qwq", "awa");
```


从哈希映射中获取和删除值.


```rust
let valueOption = hm.get(&"qwq");
let valueOption = hm.remove(&"qwq");
```


也可以使用 `for` 对哈希映射进行循环:


```rust
for (k, v) in hm { 
	println!("Key: {}, Value: {}", k, v);
}
```


哈希集合是基于哈希值的元素不重复容器, 常用于去重或快速查找元素是否存在.


```rust
let mut hs: HashSet<i32> = HashSet::new();
```


获取长度, 插入数据, 删除数据, 判断数据是否已经存在:


```rust
let len = hs.len();
let result = hs.insert(123);
let result = hs.remove(&123);
let result = hs.contains(&123);
```




<br/>


## 结构 / Structures


使用 `struct` 关键字可以创建一个结构体.


```rust
struct Point {
    x: i32,
    y: i32,
}
```


在使用结构体类型的变量时, 该变量必须被初始化.


```rust
let p : Point = Point { x: 1, y: 3 };
let p = Point { x: 1, y: 3 };
```


你可以对它的成员进行赋值, 取值.


```rust
p.x = 114514;
let x = p.x;
```


如果需要为该类型添加一些方法, 使用 `impl` 关键字. 


```rust
impl Point {
    fn new(x: i32, y: i32) -> Point {
    	Point { x: x, y: y }
    }
}
```


现在, 你可以使用 `Point::new` 来创建一个 `Point` 了.


```rust
let p = Point::new(123, 456);
```


如果你要为该类型的实例创建一些函数, 只需要在编写函数时, 将第一个参数声明为 `self` 即可.


```rust
impl Point {
    fn output(self: &Self) {
    	println!("Point, x: {}, y: {}", self.x, self.y);
    }
}
```


现在你可以通过一个 `Point` 实例来调用 `output` 函数进行输出了.


```rust
p.output();
```


在上述代码中, `self` 关键字表示当前实例, `Self` 关键字表示当前类型, 当然, 你也可以将它写成具体的类型. 下面的代码都是有效的实例函数定义:


```rust
impl Point {
    // 不使用 Self 关键字, 而是使用具体的 Point 类型
    fn output1(self: &Point) { }

    // 不使用 Self 关键字, 而是让其自动推导类型
    fn output2(&self) { }
}
```




<br/>


## 特征 / Traits


在 Rust 中, `trait` 表示某种特征. 例如 "可迭代", "可显示", "可调试". 它类似于其他编程语言的接口. 使用 `trait` 关键字创建一个特征.


```rust
trait TestTrait {
	fn some_func();
}
```


在上面的例子中, 我们创建了一个名为 `TestTrait` 的 `trait`, 它规定, 需要有一个名为 `some_func` 的无参无返回值函数.


要使某个结构实现一个 `trait`, 使用 `impl ... for ...` 语法.


```rust
impl TestTrait for Point {
    fn some_func() {
    	println!("hello world from struct Point");
    }
}
```


`trait` 主要是与泛型搭配使用. 同时和其他编程语言不一样的是, `trait` 无法直接作为一个函数的参数类型. 你需要使用泛型, 然后指定泛型需要实现某 `trait`, 然后将该泛型作为函数的参数类型使用.


`trait` 可以用内置的实现, 在这方面, 它又像其他语言的抽象类. 例如, 某个 `trait` 需要类型实现函数 A, 而函数 B 由该 `trait` 自己实现, 内部逻辑依赖于函数 A. 这时, 只要某个类型实现了这个 trait 并编写函数 A 的实现, 他就可以直接使用 `trait` 内的函数 B.


```rust
trait TestTrait {
    fn get_string(&self) -> String;
    fn print_string(&self) {
    	println!("{}", self.get_string());
    }
}

impl TestTrait for Point {
    fn get_string(&self) -> String {
    	return format!("Point, x: {}, y: {}", self.x, self.y);
    }
}
```


```rust
let p = Point::new(1, 2);
p.print_string();
```




<br/>


## 特性 / Attributes


Rust 中的特性(Attribute)是一种标记. 类似于 C# 的 `Attribute` 或者 Python 中的 `Decorators`, 在做上标记后, 即可拥有某种行为.


下面使用 `derive` 特性演示特性的使用.


```rust
#[derive(PartialEq, Eq)]
struct Point {
    x: i32,
    y: i32
}
```


在以上代码中, 我们为 Point 结构添加了 derive 特性, 这个特性用于自动实现指定的 `trait`, 在这里, 我们指定了 `PartialEq` 和 `Eq`.


在实现了 `PartialEq` 和 `Eq` 后, 我们的 Point 结构现在可以使用 `==` 和 `!=` 运算符了.


```rust
let p1 = Point { x: 123, y: 456 };
let p2 = Point { x: 345, y: 829 };

if p1 == p2 {
	println!("两点相等")
}
```




<br/>


## 枚举 / Enumerations


Rust 中的枚举和其他语言中的枚举有很大不同. 它枚举可理解为 "情况", 既可以作为类似于 C# 中的纯值类型使用, 也可以像 Java 的枚举一样在枚举中存储数据. 


Rust 对于枚举的优化是很好的, 不像 Java 一般是基于堆中存储的.


```rust
enum ColorChannel {
	Red, Green, Blue
}
```


在以上的例子中, 我们声明了一个最简单的枚举, 这个枚举仅包含三种情况, 即 '红', '绿', '蓝'. 在这种情况下, 你可以理解为我们定义了三个数字值常量, 通过 `ColorChannel` 可以访问它们.


```rust
let color_channel1 = ColorChannel::Red;
let color_channel2 = ColorChannel::Green;
let color_channel3 = ColorChannel::Blue;
```


然后使用模式匹配进行判断.


```rust
match color_channel1 {
    ColorChannel::Red => println!("is red"),
    _ => {}
}
```


在这里我们不能使用 `if` 语句进行判断, 因为我们定义的枚举没有实现名为 `PartialEq` 的 `trait`, 这是内置于 `rust` 的 `trait`, 用于重载 `==` 和 `!=` 运算符.


下面我们将以一个不同情况的颜色讲述 Rust 中枚举存储值的用法.


```rust
enum Color {
    Rgb(u8, u8, u8),
    Channel(ColorChannel)
}
```


在上面的示例中, `Color` 分成了两种情况, 一种是 `Rgb`, 一种是 `Channel`. 当是 `Rgb` 的时候, 它存储三个无符号八位整数, 当是 `Channel` 的时候, 它存储一个 `ColorChannel` 枚举.


我们可以这样使用它:


```rust
let color = Color::Rgb(89, 43, 233);

match color {
    Color::Rgb(r, g, b) => {
    	println!("颜色是 RGB 值. R: {}, G: {}, B: {}", r, g, b);
    },

    Color::Channel(channel) => {
    	println!("颜色是通道, {}", channel);
    }
}
```


> 注意, 因为这里需要将 `ColorChannel` 打印输出, 所以 `ColorChannel` 需要实现名为 `Display` 的 `trait`.


在这种有存储值的情况下, 我们也可以使用 `if let` 的语句对其进行判断:


```rust
if let Color::Rgb(r, g, b) = color {
	println!("颜色是 RGB 值. R: {}, G: {}, B: {}", r, g, b);
}
```


我们还可以为枚举中的值命名, 这样就可以:


```rust
enum Color {
    Rgb { r: u8, g: u8, b:u8 },
    Channel { channel: ColorChannel }
}
```


不过这样的话, 使用方式也需要做些改动:


```rust
let color = Color::Rgb { r: 23, g: 12, b: 129 };

match color {
    Color::Rgb { r, g, b } => {
    	println!("颜色是 RGB 值. R: {}, G: {}, B: {}", r, g, b);
    },

    Color::Channel { channel }=> {
    	println!("颜色是通道, {}", channel);
    }
}

if let Color::Rgb { r, g, b } = color {
	println!("颜色是 RGB 值. R: {}, G: {}, B: {}", r, g, b);
}
```




<br/>


## 泛型 / Generic


泛型是编程语言中极重要的一个概念. 通过使用泛型, 可以实现一些逻辑的复用. 例如, 当我们自定义的结构实现 Rust 内置的某些 `trait` 时, 我们也可以使用 Rust 内的某些函数.


下面我们将自己定义一个简单的 `trait` 和一个简单的泛型函数.


```rust
trait I32Printer {
	fn print(&self, value: i32);
}

fn print_i32<Printer: I32Printer>(value: i32, printer: Printer) {
	printer.print(value);
}
```


在上面的逻辑中, 我们在 `print_i32` 后添加了尖括号, 尖括号中, 冒号的前半部分表示需要使用的泛型类型, 冒号后面是对泛型类型的约束, 表示该泛型类型必须实现 `I32Printer` 这个 `trait`.


在参数列表中, 我们定义了 `printer` 参数, 指定其类型为我们定义的泛型类型. 这样, 它就可以接受任何实现了 `I32Printer` 的类型.


接下来我们定义两个结构, 实现 `I32Printer`, 编写不同的打印逻辑.


```rust
struct SimpleI32Printer;
struct AnotherI32Printer<'a> {
	prompt: &'a str,
}

impl I32Printer for SimpleI32Printer {
    fn print(&self, value: i32) {
    	println!("{}", value);
    }
}

impl I32Printer for AnotherI32Printer<'_> {
    fn print(&self, value: i32) {
    	println!("{}: {}", self.prompt, value);
    }
}
```


在上面的例子中, 我们定义了 `SimpleI32Printer` 和 `AnotherI32Printer` 两个结构, 并且都为它们实现了 `I32Printer` 的 `trait`.


现在, 可以调用方法, 传入不同的 printer, 然后查看运行结果了.


```rust
let printer1 = SimpleI32Printer;
let printer2 = AnotherI32Printer { prompt: "Number:" };

print_i32::<SimpleI32Printer>(114514, printer1);
print_i32::<AnotherI32Printer>(1919810, printer2);
```


可以看到, 它根据我们传入的不同类型实例, 有了不同的行为.


> 在其他语言, 例如 C# 和 Java 中, 你可以直接将接口作为参数类型指定. 但是在 Rust 中, 你必须创建一个泛型参数来做这样的逻辑.
> Rust 中, 一切参数, 变量的大小都应该是固定的. 倘若我们允许 `trait` 作为参数类型, 那么类型的大小将不再确定. 而泛型则类似于 C++ 的模板, 在编译时, Rust 编译器会对其做处理, 生成能使用多个类型进行调用的函数.


接下来就是泛型类型了, 在定义类型的时候, 我们也可以使用泛型.


```rust
struct TwoValues<T1, T2> {
    value1: T1,
    value2: T2
}
```


使用起来也很简单:


```rust
let two_values = TwoValues::<i32, u8> {
    value1: 123,
    value2: 123
};

// 或者
let two_values : TwoValues<i32, u8> = TwoValues {
    value1: 123,
    value2: 123
};

// 也可以自动推导类型, 这里将会被推导为 TwoValues<i32, i32>
let two_values = TwoValues {
    value1: 123,
    value2: 123
};
```


为泛型类型实现方法, 需要这样写:


```rust
impl<T1, T2> TwoValues<T1, T2> {
    fn common_fn(&self) {
    	println!("common func");
    }
}
```


在上面的例子中, 由于我们不知道泛型类型具体类型, 所以在 `impl` 语句后还是需要声明两个泛型类型, 然后传入到类型.


但如果你希望为带有指定泛型参数的泛型类型定义一些函数, 可以这样写:


```rust
impl TwoValues<&str, i32> {
    fn test_output(&self) {
    	println!("{}: {}", self.value1, self.value2);
    }
}
```


在上面的例子中, 因为我们只想为泛型类型参数为 `&str` 和 `i32` 的 `TwoValues` 定义函数, 泛型类型已知, 所以不必再定义泛型类型.


下面还有个例子可供参考, 第一个泛型类型参数我们指定为 `&str`, 第二个指定为实现了 `Display` 的泛型类型.


```rust
impl<T: Display> TwoValues<&str, T> {
    fn test_output2(&self) {
    	println!("{}: {}", self.value1, self.value2)
    }
}
```


需要注意的是, 与其他语言不一样, Rust 在构造类型实例或者调用泛型函数的时候, 需要使用两个冒号以及尖括号来指定泛型类型参数.


```rust
// 正确使用
let two_values = TwoValues::<&str, i32> {
    value1: "Tip",
    value2: 10
}

print_i32::<SimpleI32Printer>(114514, printer1);

// 错误使用
let two_values = TwoValues<&str, i32> {
    value1: "Tip",
    value2: 10
}

print_i32<SimpleI32Printer>(114514, printer1);
```


之所以强调这点, 是因为其他语言, 诸如 C#, Java, Kotlin, 它们在构造类型实例和调用泛型方法的时候, 都是直接使用尖括号来指定泛型类型参数的. Rust 需要多加两个冒号, 初学者可能会忘记这点.




<br/>


## 所有权 / Ownership


为了保证内存安全, Rust 引入了 '所有权' 的概念. 其大概思想为:


1. 一个类型实例有唯一的作用域, 当离开其作用域时, 该实例会被销毁
    这个作用域称为它的 '所有者', 该作用域持有该实例的 '所有权'
2. 所有权可以转交给另一个作用域, 转交后, 当前作用域将无法继续使用该实例
3. 所有权可以借用, 并且指定一定的访问权限, 当前作用域仍持有该实例的 '所有权'


大多数编程语言都有作用域的概念, 离开作用域后, 值将作废:


```rust
if true {
    let some_integer = 114514;
}

// 这里将报错, 因为已经脱离了 some_integer 的作用域
println!("value: {}", some_integer);
```


当一个值直接传入到另外一个函数中, 那么这个值的所有权也将转交到另外一个函数中:


```rust
struct MyValue {
    value: i32
}

fn print_value(value: MyValue) {
    println!("value: {}", value.value);
}

fn main() {
    let my_value = MyValue { value: 114514 };
    print_value(my_value);

    // 这里将报错, 因为在执行 print_value 的时候, 所有权已经被转让
    // 当前作用域不再持有 my_value, 也就无法再使用它
    println!("value: {}", my_value.value);
}
```


如果希望函数不转让传入参数的所有权, 可以将参数类型定义为 '引用'. 你可以将其理解为其他语言中的 '指针'. 只需要在类型前加 `&` 符号即可.


```rust
struct MyValue {
	value: i32
}

fn print_value(value: &MyValue) {
	println!("value: {}", value.value);
}

fn main() {
    let my_value = MyValue { value: 114514 };
    print_value(&my_value);

    // 这时, 你仍然可以使用 my_value
    // 因为当前作用域持有 my_value 的所有权
    println!("value: {}", my_value.value);
}
```


虽然我们将值借给了 `print_value`, 但在 `print_value` 内部, 它只能读取参数的值, 而不能对参数进行修改. 如果你希望它能够修改该实例的值, 需要在类型前添加 `mut` 关键字.


```rust
fn change_value(value: &mut MyValue) {
  value.value = 123123;
}
```


在调用时也应该使用 `&mut xxx` 来获取该实例的可修改引用.


```rust
fn main() {
    let my_value = MyValue { value: 114514 };

    change_value(&mut my_value);
}
```


如果你需要一个能够对值本身进行更改, 那么在赋值时, 需要在变量名前添加 `*` 符号.


```rust
fn change_int_value(value: &mut i32) {
    *value = 114514;
}
```




<br/>


## 错误处理


在 Rust 中, 错误分为两种: 可恢复的错误以及不可恢复的错误. 例如, 在将字符串解析为数字时, 如果数字格式不正确, 所引发的错误是程序逻辑上可以处理的. 而类似于内存访问冲突, 栈溢出这种, 就是无法恢复的错误.


不可恢复的错误会直接导致程序崩溃. 你可以使用 `panic` 宏手动引发错误.


```rust
panic!("oops");
println!("test");   // 这里代码不会被执行, 因为程序已经崩了
```


而对于可恢复的错误, Rust 中的函数都会返回一个 `Result<T, E>` 来表示可能包含错误值的返回值. 它是一个枚举, 包含两种取值: `Ok(T)` 与 `Err(E)`, 我们可以通过 `match` 语句对其两种情况分别进行处理.


```rust
let origin_str = "123";
let parse_result = origin_str.parse::<i32>();

match parse_result {
    Ok(value) => println!("Value is: {}", value),
    Err(err) => println!("Error: {}", err)
}
```


如果你确定该方法的执行不会出现错误, 也可以使用 `unwrap` 函数直接取得正确的值.


```rust
let origin_str = "123";
let parsed_value : i32 = origin_str.parse().unwrap();
```


但是如果尝试对一个错误值使用 `unwrap`, 就会引发 `panic` 了.


```rust
let origin_str = "不是数字";
let parsed_value: i32 = origin_str.parse().unwrap();   // 这里会直接崩溃, 因为解析是失败的, 无法取得结果值
```


Rust 还提供了一个 `?` 操作符用于简化异常处理. 下面的代码是不使用 `?` 的.


```rust
fn mul_input_with_10() -> Result<i32, ParseIntError> {
    let mut input = String::new();
    std::io::stdin().read_line(&mut input).unwrap();

    let valueResult = input.parse::<i32>();

    match valueResult {
        Ok(value) => Ok(value * 10),
        Err(err) => Err(err),
    }
}
```


如果使用 `?` 的话, 则是这样. 当结果为 `Err(E)` 的时候, 会直接将结果作为当前函数的返回值返回, 表达式的结果则是正确的值.


```rust
fn mul_input_with_10() -> Result<i32, ParseIntError> {
    let mut input = String::new();
    std::io::stdin().read_line(&mut input).unwrap();

    Ok(input.parse::<i32>()? * 10)
}
```




<br/>


## 模块


模块是 Rust 中组织源代码的方式. 在 Rust 中, 一个文件或者文件夹都可以叫做一个 "模块".


例如, 当我有一个 `main.rs`, 我希望在里面使用 `test.rs` 的成员时:


```rust
// 这里是 test.rs 的内容

// 公开一个函数
pub fn test_fn() {
    println!("test fn");
}
```


下面是 `main.rs`, 使用 `mod` 语句引入模块, 然后使用 `use` 语句使用模块中的成员:


```rust
// 引入 test 模块
mod test;

// 使用模块中的成员
use test::test_fn;

fn main() {
    test_fn();
}
```


如果希望将一个文件夹暴露为一个模块的话, 你需要先创建一个文件夹, 然后在文件夹下创建 `mod.rs`, 然后编写内容. 在该文件下向外暴露的成员, 即为该模块的成员.


```rust
// 这里是 test2/mod.rs 的内容

// 公开一个函数
pub fn test_fn() {
    println!("test fn");
}
```


引用的时候和之前的代码一样, 只需要使用 `mod test2` 即可引入 `test2` 模块.


如果你希望在 `test2`  文件夹下编写更多的文件, 并向外暴露:


```txt
|- test2
|  |- another.rs
|  -- mod.rs
|
-- main.rs
```


那么任何你想要向外暴露的内容, 都应该在 `test2/mod.rs` 下声明好.


```rust
// 这里是 test2/another.rs 的内容

// 公开一个结构
pub struct AnotherStruct {
    
}
```


在 `test2/mod.rs` 中, 你需要导入并公开 `another` 这个模块.


```rust
// 这里是 test2/mod.rs 的内容

// 导入并公开 another 模块
pub mod another;

// 公开属于 test2 的成员
pub fn test_fn() {
    println!("test fn");
}
```


于是, 你就可以在 `main.rs` 中, 使用 `AnotherStruct` 这个类型了.


```rust
// 导入 test2 模块
mod test2;

// 使用 test2/another 中的 AnotherStruct
use test2::another::AnotherStruct;

fn main() {
    let value = AnotherStruct {};
}
```


但是, 如果你希望在使用 `AnotherStruct` 时, 直接通过 `test2::AnotherStruct` 导入, 也可以在 `mod.rs` 这样向外公开:


```rust
// 这里是 test/mod.rs 的内容

// 导入 another 模块, 但是不公开
mod another;

// 使用并公开 another 下的结构
pub use another::AnotherStruct;
```


这样 `AnotherStruct` 可以通过 `use` 语句直接向外暴露, 使用时就可以直接 `use test2::AnotherStruct` 了


```rust
// 导入 test2 模块
mod test2;

// 使用 test2 直接暴露的 AnotherStruct
use test2::AnotherStruct;

fn main() {
    let value = AnotherStruct {};
}
```


方便起见, 你也可以直接用 `*` 在 `mod.rs` 直接向外暴露某个模块的所以成员:


```rust
mod another;

// 向外暴露 another 中的所有成员
pub use another::*;
```


如果你在使用多个模块时, 它们的类型名称相同, 你可以在 `use` 的使用, 使用 `as` 为其取别名:


```rust
mod test2;

use test2::AnotherStruct as qwq;

fn main() {
    let value = qwq {};
}
```

