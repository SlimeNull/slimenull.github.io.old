---
title: '[教程] 在 Y 分钟内学会 Python'
date: 2021-05-05 18:44:21
tags:
  - python
categories:
  - 笔记
  - 成长记录
  - 灌水
description: '在 Y 分钟内学会 Python这是翻译, 原文地址: Learn Python in Y Minutes在 90 年代初, Python 由 Guido van Rossum 创造, 现在, 它是最受欢迎的编程语言之一. 因其简明的语法, 我爱上了它. 语法基本上是可以执行的伪代码.提示: 这篇文章适用于 Python 3, 如果你想要学习旧版 Python 2.7, 单击这里# 单行注释以 '#' 作为开头"""多行注释可以使用三个双引号   并且经常用与书写文档"""#####'
---

# 在 Y 分钟内学会 Python


> 这是翻译, 原文地址: [Learn Python in Y Minutes](https://learnxinyminutes.com/docs/python/)


在 90 年代初, Python 由 Guido van Rossum 创造, 现在, 它是最受欢迎的编程语言之一. 因其简明的语法, 我爱上了它. 语法基本上是可以执行的伪代码.


提示: 这篇文章适用于 Python 3, 如果你想要学习旧版 Python 2.7, 单击[这里](http://learnxinyminutes.com/docs/pythonlegacy/)


```python
# 单行注释以 '#' 作为开头

"""多行注释可以使用三个双引号
   并且经常用与书写文档
"""

####################################################
## 1. 原始数据类型和操作符
####################################################

# 你可以使用数字
3  # 等同于 3

# 数学运算也是你希望使用的形式
1 + 1  # 结果是 2
8 - 1  # 结果是 7
10 * 2  # 结果是 20
35 / 5  # 结果是 7.0

# 正负数的整除都会向下取整
5 // 3  # 结果是 1
-5 // 3  # 结果是 -2
5.0 // 3.0  # 结果是 1.0 # 在浮点运算中也同样生效
-5.0 // 3.0  # 结果是 -2.0

# 除法的运算结果始终是浮点数
10.0 / 3  # 结果是 3.3333333333333335

# 取模运算
7 % 3  # 结果是 1
# i % j 结果的符号与 j 相同, 这与 C 语言不同
-7 % 3  # 结果是 2

# 幂运算 (x**y, x的y次方)
2 ** 3  # 结果是 8

# 用括号来强制优先运算
1 + 3 * 2  # 结果是 7
(1 + 3) * 2  # 结果是 8

# 布尔值是基本数据类型
True  # 值为 真(true)
False  # 值为 假(false)

# 使用 not 来进行非运算
not True  # 结果是 假
not False  # 结果是 真

# 布尔值操作符:
# 提示, 'and' 和 'or' 是区分大小写的
True and False  # 结果是 假
False or True  # 结果是 真

# True 和 False 事实上也等同于 1 和 0, 只不过是使用了不同的关键字
True + True  # 结果是 2
True * 8  # 结果是 8
False - 5  # 结果是 -5

# 比较运算符会检查 True 和 False 的数字值
0 == False  # 结果是 真
1 == True  # 结果是 真
2 == True  # 结果是 假
-5 != False  # 结果是 真

# 对整数使用布尔逻辑操作符, 则会将它们转换为布尔值以求值，但返回未转换的值
# 不要把 bool(ints) 和 位运算 and/or 搞混了
bool(0)  # 返回 假
bool(4)  # 返回 真
bool(-6)  # 返回 真
0 and 2  # 返回 0
-5 or 0  # 返回 -5

# 运算符 '等同于' 是 ==
1 == 1  # 返回 真
2 == 1  # 返回 假

# 运算符 '不等于' 是 !=
1 != 1  # 返回 假
2 != 1  # 返回 真

# 更多比较运算符
1 < 10  # 返回 真
1 > 10  # 返回 假
2 <= 2  # 返回真
2 >= 2  # 返回真

# 检查一个值是否在指定范围内
1 < 2 and 2 < 3  # 返回 真
1 < 3 and 3 < 2  # 返回 假
# 连接起来, 这样看起来会更好看些
1 < 2 < 3  # 返回 真
2 < 3 < 2  # 返回 假

# (is 与 ==) is 将会检查两个变量是否引用了同一个对象, 但是 == 检查
# 两个对象是否指向了相同的值
a = [1, 2, 3, 4]  # 使 a 指向一个新的列表, [1, 2, 3, 4]
b = a  # 使 b 指向 a 所指向的对象
b is a  # 返回 真, a 和 b 引用的是同一个对象
b == a  # 返回 真, a 和 b 的对象是相等的
b = [1, 2, 3, 4]  # 使 b 指向一个新的列表, [1, 2, 3, 4]
b is a  # 返回 假, a 与 b 并不引用同一个对象
b == a  # 返回 真, a 和 b 的对象使相等的

# 字符串可以使用 双引号 " 或 单引号 ' 来创建
"这是一个字符串"
'这也是一个字符串'

# 字符串可以相加
"Hello " + "world!"  # 返回 "Hello world!"
"Hello " "world!"  # 等同于 "Hello world!"

# 字符串可以用作一个字符列表
"Hello world!"[0]  # 返回 'H'

# 你可以获取字符串的长度:
len("这是一个字符串")  # 返回 7

# 你可以使用 f-字符串 或 格式化字符串 来对字符串文本进行格式化 (在 Python 3.6 或更高版本)
name = "小红"
f"她说她的名字是{name}."  # 返回 "她说她的名字是小红."
# 你可以基本的将 Python 表达式放到括号内, 然后它就会被输出到字符串中.
f"{name}是{len(name)}字符长."  # 返回 "小红是两个字符长"

# None 是一个对象
None  # 返回 None

# 不要使用等同于运算符 '==' 来比较对象和 None
# 使用 'is' 来代替. 这个会检查对象标识是否相同.
"etc" is None  # 返回 假
None is None  # 返回 真

# None, 0, 以及空的字符串/列表/字典/元组, 都计算为 假
bool(0)  # => 假
bool('')  # => 假
bool([])  # => 假
bool({})  # => 假
bool(())  # => 假

####################################################
## 2. 变量和集合
####################################################

# Python 有一个 print 函数, 用于标准输出
print("我是Python, 很高兴见到你!")  # => 我是Python, 很高兴见到你!

# 默认的, print 函数还会在结尾输出一个换行符
# 使用可选参数 'end' 来改变末尾的字符串.
print("Hello, world", end="!")  # => Hello, world!   # 输出后没有换行

# 从控制台获取输入数据的简单方式:
input_string_var = input("Enter some data:")  # 以字符串的形式返回输入数据

# Python 中没有变量的声明, 只有赋值.
# 命名规范是使用小写以及下划线, 就像这样: lower_case_with_underscores
some_var = 5
some_var  # => 5

# 访问一个没有生命的变量是错误的
# 查看控制流来获取更多异常捕捉信息
some_unknown_var  # 这是一个一个未定义的变量, 运行时将抛出 NameError 异常

# if 也可用作一个表达式
# 等同于 C 语言中的 '?:' 三元运算符
"yay" if 0 > 1 else "nay!"  # => "nay"

# 列表可以存储一个序列, 可以这样定义:
li = []
# 你可以为其指定初始值
other_li = [4, 5, 6]

# 使用 append 方法在列表的末尾添加一些什么东西
li.append(1)  # 现在 li 的值是 [1]
li.append(2)  # 现在 li 的值是 [1, 2]
li.append(4)  # 现在 li 的值是 [1, 2, 4]
li.append(3)  # 现在 li 的值是 [1, 2, 4, 3]
# 使用 pop 方法从列表的末尾移除元素
li.pop()  # 返回 3, 并且现在 li 的值是 [1, 2, 4]
# 重新将它放回去
li.append(3)  # 现在 li 又变成 [1, 2, 4, 3] 了

# 像访问数组一样访问一个列表
li[0]  # => 1
# 访问列表的最后一个元素
li[-1]  # => 3

# 如果索引超出界限, 那么会抛出 IndexError 异常
li[4]  # 抛出 IndexError 异常

# 你可以使用切片语法来查看一个范围内的元素
# 起始索引包含在内, 但结束索引不包含在内
# (对于数学类型来讲, 它是一个 闭/开 区间)
li[1:3]  # 返回从索引1到3的一个列表 => [2, 4]
li[2:]  # 返回从索引2开始的列表 => [4, 3]
li[:3]  # 返回从开始到索引3的列表 => [1, 2, 4]
li[::2]  # 返回一个每两个元素选择一个的列表 => [1, 4]
li[::-1]  # 返回反向排列的列表 => [3, 4, 2, 1]
# 使用任意组合来实现高级切片
# li[起始:结束:步长]

# 使用切片来创建一个单层的深度拷贝
li2 = li[:]

# 使用 "del" 来删除任意元素
del li[2]  # 现在 li 的值是 [1, 2, 3]

# 删除第一个匹配值的元素
li.remove(2)  # 现在 li 的值是 [1, 3]
li.remove(2)  # 抛出 ValueError 异常, 2 并不在这个列表中

# 在指定索引处插入元素, 列表.insert(索引, 元素)
li.insert(1, 2)  # 现在 li 的值又是 [1, 2, 3] 了

# 获取第一个匹配元素的索引
li.index(2)  # => 1
li.index(4)  # 抛出 ValueError 异常, 4 不在这个列表中

# 你可以将列表相加
# 提示: values 和 other_li 的值不会被修改
li + other_li  # => [1, 2, 3, 4, 5, 6]

# 使用 "extend()" 连接列表, extend 的意思是拓展
li.extend(other_li)  # 现在 li 的值是 [1, 2, 3, 4, 5, 6]

# 使用 "in" 检查元素是否存在于列表中
1 in li  # => True

# 使用 "len()" 检查列表的长度
len(li)  # => 6

# 元组与列表相像, 但是不可更改
tup = (1, 2, 3)
tup[0]  # => 1
tup[0] = 3  # 抛出一个 TypeError

# 提示, 长度为一的元组的最后一个元素必须有一个逗号跟随, 但是
# 其他长度的元组, 尽管是0, 也不需要
type((1))  # => <class 'int'>
type((1,))  # => <class 'tuple'>
type(())  # => <class 'tuple'>

# 大多数的列表操作符都可以在元组上使用
len(tup)  # => 3
tup + (4, 5, 6)  # => (1, 2, 3, 4, 5, 6)
tup[:2]  # => (1, 2)
2 in tup  # => True

# 你可以将元组(或者列表)解包为变量
a, b, c = (1, 2, 3)  # 现在, a 是 1, b 是 2, c 是 3
# 你还可以使用拓展解包
a, *b, c = (1, 2, 3, 4)  # 现在, a 是 1, b 是 [2, 3], c 是 4
# 默认情况下, 即便你忽略括号, 也会创建一个元组
d, e, f = 4, 5, 6  # 元组 4, 5, 6 被解包为变量 d, e, f
# 变量值分别如下: d = 4, e = 5 以及 f = 6
# 那么看看交换两个值是多么的简单
e, d = d, e

# 字典用于存储从键到值的映射
empty_dict = {}  # 创建了一个空字典
# 下面是一个带有初始值的字典
filled_dict = {"one": 1, "two": 2, "three": 3}

# 提示: 字典的键必须是不可变的类型.  这是为了确保
# 键能够转换为用于快速查询的常量哈希值.
# 不可变类型包含整数, 浮点数, 字符串, 元组
invalid_dict = {[1, 2, 3]: "123"}  # 抛出 TypeError: unhashable type: 'list' 异常. (类型错误: 无法进行哈希化的类型: '列表')
valid_dict = {(1, 2, 3): [1, 2, 3]}  # 然而, 值可以是任何类型

# 使用 [] 来查询值
filled_dict["one"]  # => 1

# 使用 "keys()" 来获取所有的键并作为一个可迭代对象返回. 我们需要在 list() 中将调用结果转换
# 为一个列表. 这个稍后再讲. 提示 - 在 Python 低于 3.7 的版本中
# 字典键索引的顺序是无法保证的. 你的结果可能不与下面的例子完全相等. 然而, 在 Python 3.7 中, 字典
# 元素会保持它们被插入到字典的顺序
list(filled_dict.keys())  # => ["three", "two", "one"] 在低于 3.7 的 Python 版本中
list(filled_dict.keys())  # => ["one", "two", "three"] 在 3.7 或更高的 Python 版本中

# 使用 "values()" 来获取所有的值并作为可迭代对象返回. 同样, 我们需要将其在
# list() 中转换, 以取出这个可迭代对象的值. 提示 - 和上面键的顺序是一样的
list(filled_dict.values())  # => [3, 2, 1] 在低于 3.7 的 Python 版本中
list(filled_dict.values())  # => [1, 2, 3] 在 3.7 或更高的 Python 版本中

# 使用 "in" 来检查键是否在字典中
"one" in filled_dict  # => True
1 in filled_dict  # => False

# 通过不存在的键来查找字典, 会抛出 KeyError 异常
filled_dict["four"]  # KeyError

# 使用 "get()" 方法来避免 KeyError 异常
filled_dict.get("one")  # => 1
filled_dict.get("four")  # => None
# 这个方法支持当找不到值时返回指定的默认值
filled_dict.get("one", 4)  # => 1
filled_dict.get("four", 4)  # => 4

# "setdefault()" 只有在给定键不存在的时候将值插入到字典
filled_dict.setdefault("five", 5)  # filled_dict["five"] 被设置为了 5
filled_dict.setdefault("five", 6)  # filled_dict["five"] 仍然是 5

# 向字典中添加内容
filled_dict.update({"four": 4})  # => {"one": 1, "two": 2, "three": 3, "four": 4}
filled_dict["four"] = 4  # 向字典中添加的另一种方式

# 自 Python 3.5 以来, 你还可以使用拓展解包选项
{'a': 1, **{'b': 2}}  # => {'a': 1, 'b': 2}
{'a': 1, **{'a': 2}}  # => {'a': 2}

# 集合用来存储 ... 额, 集合
empty_set = set()  # 空集合
# 用一组值来初始化一个集合, 嗯, 看起来有点像字典, 抱歉
some_set = {1, 1, 2, 2, 3, 4}  # some_set 现在的值是 {1, 2, 3, 4}

# 与字典中的键相似, 集合的元素必须是不可变的
invalid_set = {[1], 1}  # => 抛出一个 TypeError: unhashable type: 'list' (类型错误: 无法进行哈希化的类型: '列表')
valid_set = {(1,), 1}

# 向集合中添加一个或多个条目
filled_set = some_set
filled_set.add(5)  # filled_set 现在是 {1, 2, 3, 4, 5}
# 集合是不包含重复元素的
filled_set.add(5)  # 还是与之前一样 {1, 2, 3, 4, 5}

# 使用 & 取交集
other_set = {3, 4, 5, 6}
filled_set & other_set  # => {3, 4, 5}

# 使用 | 取并集
filled_set | other_set  # => {1, 2, 3, 4, 5, 6}

# 使用 - 取差集
{1, 2, 3, 4} - {2, 3, 5}  # => {1, 4}

# 使用 ^ 取对称差集
{1, 2, 3, 4} - {2, 3, 5}  # => {1, 4, 5}

# 检查左侧的集合是否是右侧集合的超集
{1, 2} >= {1, 2, 3}  # => False

# 检查左侧的集合是否是右侧集合的子集
{1, 2} <= {1, 2, 3}  # => True

# 使用 in 来检查是否存在于集合中
2 in filled_set  # => True
10 in filled_set  # => False

# 生成一个单层的深层副本
filled_set = some_set.copy()  # filled_set 是 {1, 2, 3, 4, 5}
filled_set is some_set  # => False

####################################################
## 3. 控制流和可迭代对象
####################################################

# 首先我们声明一个变量
some_var = 5

# 这是一个 if 语句, 缩进在 Python 中非常重要
# 约定语法是使用四个空格, 而不是水平制表符(tab)
# 这个将会打印 "some_var 比 10 小"
if some_var > 10:
    print("some_var 比 10 大")
elif some_var < 10:  # 这个 elif 语句是可选的
    print("some_var 比 10 小")
else:  # 这个也是可选的
    print("some_var 与 10 相等")

"""
for 语句用来循环遍历列表
将会打印:
    狗是哺乳动物
    猫是哺乳动物
    老鼠是哺乳动物
"""
for animal in ["狗", "猫", "老鼠"]:
    # 你可以使用 format() 来插入格式化字符串
    print("{}是哺乳动物".format(animal))

"""
"range(数字)" 返回一个数字的可迭代对象
从0到给定数字
将会打印:
    0
    1
    2
    3
"""
for i in range(4):
    print(i)

"""
"range(较小的数, 较大的数)" 返回一个数字的可迭代对象
从较小的数字到较大的数字
将会打印:
    4
    5
    6
    7
"""
for i in range(4, 8):
    print(i)

"""
"range(较小的数, 较大的数, 步长)" 返回一个数字的可迭代对象
从较小的数到较大的数, 以步长未每次增长的值
如果步长没有指定, 默认值则是 1
将会打印:
    4
    6
"""
for i in range(4, 8, 2):
    print(i)

"""
循环一个列表, 并且同时检索列表中每一个条目的索引和值
将会打印:
    0 狗
    1 猫
    2 老鼠
"""
animals = ["狗", "猫", "老鼠"]
for i, value in enumerate(animals):
    print(i, value)

"""
while 循环将一直进行到条件不再满足为止
将会打印:
    0
    1
    2
    3
"""
x = 0
while x < 4:
    print(x)
    x += 1  # x = x + 1 的简写

# 使用 try/except 语句块来处理异常
try:
    # 使用 "raise" 来抛出异常
    raise IndexError("这是一个索引错误")
except IndexError as e:
    pass  # pass 只是一个占位符(不进行任何操作). 通常你需要在这里对异常进行处理
except (TypeError, NameError):
    pass  # 如果需要, 你可以同时处理多个异常.
else:  # try/except 语句块的可选语句. 必须在所有 except 语句块的后面
    print("一切正常!")  # 仅在 try 语句内没有任何异常时运行
finally:  # 在任何情况下都会执行
    print("我们可以在这里进行资源清理")

# 你可以使用 with 语句代替 try/finally 来清理资源
with open("我的文件.txt") as f:
    for line in f:
        print(line)

# 向文件中写入内容
contents = {"aa": 12, "bb": 21}
with open("我的文件1.txt", "w+") as file:
    file.write(str(content))  # 向文件中写入字符串

with open("我的文件2.txt", "w+") as file:
    file.write(json.dumps(content))  # 向文件中写入一个对象

# 从文件中读取
with open("我的文件1.txt", "r+") as file:
    contents = file.read()  # 从文件中读取一个字符串
print(contents)
# 打印: {"aa", 12, "bb": 21}

with open("我的文件2.txt", "r+") as file:
    contents = json.load(file)  # 从文件中读取一个json对象
print(contents)
# print: {"aa": 12, "bb": 21}


# Python 提供了一个叫做 Iterable(可迭代的) 的基本抽象
# 一个可迭代对象是一个可视为序列的对象
# range 函数返回的对象就是一个可迭代对象

filled_dict = {"one": 1, "two": 2, "three": 3}
our_iterable = filled_dict.keys()
print(our_iterable)  # => dict_keys(['one', 'two', 'three']). 这是一个实现了 Iterable 接口的对象

# 我们可以检索它
for i in our_iterable:
    print(i)  # 打印 one, two, three

# 然而, 我们不可以通过索引来找到元素
our_iterable[1]  # 抛出 TypeError 异常

# 一个可迭代对象即为能够创建迭代器的对象
our_iterator = iter(our_iterable)

# 迭代器是一个能够记住当前通过它迭代状态的对象
# 我们可以通过 "next()" 来获取下一个对象
next(our_iterator)  # => "one"

# 它会保持我们遍历的状态
next(our_iterator)  # => "two"
next(our_iterator)  # => "three"

# 在迭代器已经返回完所有的数据后, 将会抛出 StopIteration 异常
next(our_iterator)  # 抛出 StopIteration 异常

# 我们也可以检索它, 事实上, "for" 语句就是隐式的执行了这个操作
our_iterator = iter(our_iterable)
for i in iterator:
    print(i)

# 你可以通过调用 list() 来获取可迭代对象或迭代器的所有元素.
list(our_iterable)  # => 返回 ["one", "two", "three"]
list(our_iterator)  # => 返回 [] 因为迭代状态已经被保存下来


####################################################
## 4. 函数
####################################################

# 使用 "def" 来创建一个新的函数
def add(x, y):
    print("x 是 {} 以及 y is {}".format(x, y))
    return x + y


# 调用带参数的函数
add(5, 6)  # => 输出 "x 是 5 以及 y 是 6", 并返回 11

# 调用函数的另一种方式是带有关键字参数
add(y=6, x=5)  # 关键字参数可以在任何顺序下正常运行


# 你可以定义接受数量可变的位置参数的函数
def varargs(*args):
    return args


varargs(1, 2, 3)  # => (1, 2, 3)


# 同样, 你可以定义接受数量可变的关键字参数的函数
def keyword_args(**kwargs):
    return kwargs


# 来调用一下, 然后看看会发生什么
keyword_args(big="foot", loch="ness")  # => {"big": "foot", "loch": "ness"}


# 只要你想, 你也可以同时使用它们两个
def all_the_args(*args, **kwargs):
    print(args)
    print(kwargs)


"""
all_the_args(1, 2, a=3, b=4) 将会打印:
    (1, 2)
    {"a": 3, "b": 4}
"""

# 在调用函数时, 你可以做相反的 args/kwargs!
# 使用 * 来拓展元组, 以及使用 ** 来拓展 kwargs.
args = (1, 2, 3, 4)
kwargs = {"a": 3, "b": 4}
all_the_args(*args)  # 等同于 all_the_args(1, 2, 3, 4)
all_the_args(**kwargs)  # 等同于 all_the_args(a=3, b=4)
all_the_args(*args, **kwargs)  # 等同于 all_the_args(1, 2, 3, 4, a=3, b=4)


# 返回多个值(通过赋值元组)
def swap(x, y):
    return y, x  # 通过没有括号的元组来返回多个值
    # (提示: 括号虽然没有写, 但是也可以添加上)


x = 1
y = 2
x, y = swap(x, y)  # => x = 2, y = 1
# (x, y) = swap(x,y)  # 同样, 括号虽没有写, 但是也可以添加上

# 函数作用域
x = 5


def set_x(num):
    # 局部变量 x 与全局变量 x 是不同的
    x = num  # => 43
    print(x)  # => 43


def set_global_x(num):
    global x
    print(x)  # => 5
    x = num  # => 全局变量 x 现在被设置为 6 了
    print(x)  # => 6


set_x(43)
set_global_x(6)


# Python 支持本地函数
def create_adder(x):
    def adder(y):
        return x + y

    return adder


add_10 = create_adder(10)
add_10(3)  # => 13

# 也支持匿名函数
(lambda x: x > 2)(3)  # => True
(lambda x, y: x ** 2 + y ** 2)(2, 1)  # => 5

# 下面是一些内置的高阶函数
list(map(add_10, [1, 2, 3]))  # => [11, 12, 13]
list(map(max, [1, 2, 3], [4, 2, 1]))  # => [4, 2, 3]

list(filter(lambda x: x > 5, [3, 4, 5, 6, 7]))  # => [6, 7]

# 你可以使用列表推导式来实现优秀的映射与过滤
# 列表推导式存储可嵌套的列表以输出
[add_10(i) for i in [1, 2, 3]]  # => [11, 12, 13]
[x for x in [3, 4, 5, 6, 7] if x > 5]  # => [6, 7]

# 同样, 你可以构建集合和字典推导式
{x for x in 'abcddeef' if x not in 'abc'}  # => {'d', 'e', 'f'}
{x: x ** 2 for x in range(5)}  # => {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}

####################################################
## 5. 模块
####################################################

# 导入模块, 调用模块中的函数
import math

print(math.sqrt(16))  # => 返回4.0

# 你可以从模块中获取指定的函数
from math import ceil, floor

print(ceil(3.7))  # => 4.0
print(floor(4.7))  # => 3.0

# 你可以从模块中导入所有函数
# 警告: 不推荐这么做
from math import *

# 你可以在导入模块时, 为其起一个别名
import math as m

math.sqrt(16) == m.sqrt(16)  # => True

# Python 模块只是普通的 Python 文件. 你
# 可以编写你自己的模块, 然后导入它们. 模块
# 的名字与文件名是相同的

# 你可以查找模块内定义的函数和属性
import math

dir(math)


# 如果有一个名为 math.py 的 Python 脚本与
# 当前脚本在同一个目录下, 那么这个文件将会
# 代替 Python 内建模块以加载.
# 这是因为本地目录有比 Python 的内建库更高
# 的优先级.


####################################################
## 6. 类
####################################################

# 使用 "class" 语句来创建一个类
class Human:
    # 类的属性. 将在类的所有实例中共享
    species = "SlimeNull"

    # 基本的初始化器, 这个将会在类实例化时调用
    # 提示, 双前置和后置下划线表示 Python 使用
    # 的对象或属性, 但是它们存在于用户控制的命名
    # 空间. 方法(或者是对象, 属性) 像 __init__, __str__,
    # __repr__ 等等, 被称为特殊方法(有时也成为魔法方法)
    # 你不应该创建你自己的, 与它们重名的成员
    def __init__(self, name):
        # 将参数赋值给实例的 name 属性
        self.name = name

        # 初始化属性
        self._age = 0

    # 实例的方法. 所有的方法都需要使用 "self" 作为第一个参数
    def say(self, msg):
        print("{name}: {message}".format(name=self.name, message=msg))

        # 实例的另一个方法
        def sing(self):
            return '哟... 哟... 麦克风检查... one... one two...'

    # 类的方法, 所有的实例都能够访问
    # 它们被调用时, 调用它的类将作为调用的第一个参数
    @classmethod
    def get_species(cls):
        return cls.species;

    # 静态方法在调用时, 不会有类或实例的引用传入
    @staticmethod
    def grunt():
        return "*咕噜咕噜*"

    # 属性就像一个获取器(getter)
    # 它将这个方法 age() 作为一个同名的, 只读的属性返回
    # 但是, 在 Python 中不需要写繁琐的获取器和设置器(setter)
    @property
    def age(self):
        return self._age

    # 这个将允许属性被设置
    @age.setter
    def age(self, age):
        self._age = age

    # 这个将允许属性被删除
    @age.deleter
    def age(self):
        del self._age


# 当 Python 解释器读取源文件并执行时
# __name__ 会确保这个代码块在模块中
# 是作为主程序执行的
if __name__ == '__main__':
    # 实例化类
    i = Human(name="小明")
    i.say("嗨~")
    j = Human("小红")
    j.say("你好哇~")
    # i 和 j 是类型 Human 的实例, 换句话说, 他们都是 Human 对象

    # 调用类的方法
    i.say(i.get_species())          # => "小明: SlimeNull"
    # 更改共享属性
    Human.species = "Little Wu♂ Fairy"
    i.say(i.get_species())          # => "小明: Little Wu♂ Fairy"
    j.say(j.get_species())          # => "小红: Little Wu♂ Fairy"

    # 调用静态方法
    print(Human.grunt())            # => "*咕噜咕噜*"

    # 静态方法也可以通过实例来调用
    print(i.grunt())                # => "*咕噜咕噜*"

    # 为这个实例更新属性
    i.age = 42
    # 获取属性
    i.say(i.age)                    # => "小明: 42"
    j.say(j.age)                    # => "小红: 0"
    # 删除属性
    del i.age
    # i.age                         # => 将会抛出 AttributeError 异常


####################################################
## 6.1 继承
####################################################

# 继承允许定义的新的子类从父类继承
# 方法与变量

# 使用上面定义的 Human 类作为基类(父类), 我们可以定义
# 一个子类, Superhero, 它将继承 Human 类的变量, 例如
# "species", "name", 以及 "age", 方法也是如此, 例如
# "sing" 与 "grunt". 但同时它也可以有自己的特殊属性.

# 你可以将上面的类存储到它们自己的文件中来采用模块化文件的优点
# 命名为, human.py

# 从别的文件中导入函数, 需要使用下面的格式
# from "没有后缀的文件名" import "函数或者类"

from human import Human


# 在类型定义处以参数的形式指定父类
class Superhero(Human):

    # 如果字类仅仅是从父类继承所有定义, 且没有
    # 任何更改, 你可以只在这里写一个 "pass" 关键字 (别的不需要写)
    # 但在我们这种情况下, pass 就要被注释掉以为 Superhero 类创建
    # 它特有的内容
    # pass

    # 字类可以覆盖父类的属性
    species = "Superhuman"

    # 字类会自动的从父类继承构造函数, 包括
    # 它的参数, 但是也可以定义另外的参数, 或者定义
    # 然后覆盖它的方法, 例如这个类的构造函数
    # 这个构造函数从 Human 类继承了 "name" 参数并且
    # 添加了 "superpower" 和 "movie" 参数
    def __init__(self, name, movie=False,
                 superpowers = ["力大无穷", "金刚不坏之身"]):

        # 添加额外的类型属性
        self.fictional = True
        self.movie = movie
        # 注意可变的默认值, 因为它们默认是共享的
        self.superpowers = superpowers

        # "super" 函数使你可以访问被字类重写了的
        # 父类的方法, 在这里, 我们要使用 "__init__"
        # 这将会调用父类的构造函数
        super().__init__(name)

    # 覆盖 sing 方法
    def sing(self):
        return "Dun, dun, DUN!"

    # 添加附加的实例方法
    def boast(self):
        for power in self.superpowers:
            print("我有{pow}的能力!".format(pow=power))


if __name__ == '__main__':
    sup = Superhero(name="蒂克")

    # 实例类型检查
    if isinstance(sup, Human):
        print("我是人类")
    if type(sup) is Superhero:
        print("我是一个超级英雄")

    # 通过使用 getattr() 和 super() 来获取方法解析搜索顺序
    # 这个属性是动态的, 并且可以被更新
    print(Superhero.__mro__)    # => (<class '__main__.Superhero'>,
                                # => <class 'human.Human'>, <class 'object'>)

    # 通过它自己的类型属性来调用父类的方法
    print(sup.get_species())    # => Superhuman

    # 调用被重写了的方法
    print(sup.sing())           # => Dun, dun, DUN!

    # 调用 Human 的方法
    sup.say("勺子?")             # => 蒂克: 勺子?

    # 调用仅存在于 Superhero 中的方法
    sup.boast()                 # => 我有力大无穷的能力!
                                # => 我有金刚不坏之身的能力!

    # 继承的类型属性
    sup.age = 32
    print(sup.age)              # => 32

    # 仅在 Superhero 中存在的属性
    print('我能获得奥斯卡奖吗?' + str(sup.movie))

####################################################
## 6.2 多重继承
####################################################

# 另一个类的定义
# bat.py
class Bat:

    species = '贝蒂'

    def __init__(self, can_fly=True):
        self.fly = can_fly

    # 这个类也有一个说的方法
    def say(self, msg):
        msg = '... ... ...'
        return msg

    # 并且它也有自己的方法
    def sonar(self):
        return '))) ... ((('

if __name__ == '__main__':
    b = Bat()
    print(b.say('你好'))
    print(b.fly)


# 然后现在另一个类型定义继承自 Superhero 和 Bat
# superhero.py
from superhero import Superhero
from bat import Bat

# 定义 Batman 并同时继承 Superhero 和 Bat
class Batman(Superhero, Bat):

    def __init__(self, *args, **kwargs):
        # 继承属性通常需要 super
        # super(Batman, self).__init__(*args, **kwargs)
        # 然而在这里处理多继承, 所以 super()
        # 仅仅适用于 MRO 列表的下一个基类
        # 所以, 我们需要为所有父类显式的调用 __init__
        # *args 和 **kwargs 允许使用非常简洁的方式传递所有参数,
        # 每一个父类都 "给洋葱剥一层皮"
        Superhero.__init__(self, 'anonymous', movie=True,
                           superpowers=['Wealthy'], *args, **kwargs)
        Bat.__init__(self, *args, can_fly=False, **kwargs)
        # 覆盖相同名称的属性的值
        self.name = "悲伤的阿弗莱克"

    def sing(self):
        return "呐 呐 呐 呐 呐 蝙蝠侠!"


if __name__ == '__main__':
    sup = Batman()

    # 通过使用 getattr() 和 super() 来获取方法解析搜索顺序
    # 这个属性是动态的, 并且可以被更新
    print(Batman.__mro__)       # => (<class '__main__.Batman'>,
                                # => <class 'superhero.Superhero'>,
                                # => <class 'human.Human'>,
                                # => <class 'bat.Bat'>, <class 'object'>)

    # 通过它自己的类型属性来调用父类的方法
    print(sup.get_species())    # => Superhuman

    # 调用被重写了的方法
    print(sup.sing())           # => 呐 呐 呐 呐 呐 蝙蝠侠!

    # 调用 Human 的方法, 原因是继承问题
    sup.say('我同意')            # => 悲伤的阿弗莱克: 我同意

    # 调用存在于第二个祖先的方法
    print(sup.sonar())          # => ))) ... (((

    # 继承了的类型属性
    sup.age = 100
    print(sup.age)              # => 100

    # 从第二个祖先继承的默认值被重写了的属性
    print('我能飞吗?' + str(sup.fly))  # => 我能飞吗? 不能



####################################################
## 7. 高级
####################################################

# 生成器可以帮助你写一些更简便的代码 (偷懒)
def double_numbers(iterable):
    for i in iterable:
        yield i + i

# 生成器是 高效内存 的, 因为它们只加载需要的数据
# 处理可迭代对象的下一个值, 这使他们可以在非常大
# 的值域上执行操作.
# 提示: `range` 在 Python3 中取代了 `xrange`
for i in double_numbers(range(1, 900000000)):  # `range` 是一个生成器
    print(i)
    if (i > 30):
        break

# 就像你能创建列表推导式一样, 你也可以创建
# 生成器推导式
values = (-x for x in [1,2,3,4,5])
for x in values:
    print(x)  # 打印 -1 -2 -3 -4 -5 到 控制台/终端

# 你也可以将生成器推导式转换为一个列表
values = (-x for x in [1,2,3,4,5])
gen_to_list = list(values)
print(gen_to_list)  # => [-1, -2, -3, -4, -5]

# 装饰器
# 在这个例子中, `beg` 与 `say` 交换. 如果 say_please 是 True, 那么它
# 将会改变返回的消息
from functools import wraps


def beg(target_function):
    @wraps(target_function)
    def wrapper(*args, **kwargs):
        msg, say_please = target_function(*args, **kwargs)
        if say_please:
            return "{} {}".format(msg, "求你了, 我很穷 :(")
        return msg

    return wrapper


@beg
def say(say_please=False):
    msg = "能为我买瓶啤酒吗?"
    return msg, say_please


print(say())                 # 能为我买瓶啤酒吗?
print(say(say_please=True))  # 能为我买瓶啤酒吗? 求你了, 我很穷 :(
```


