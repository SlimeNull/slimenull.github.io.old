---
title: '[PowerShell] 快速入门, 基本语法, 常用类型, 函数, .NET 互操作'
slug: '[PowerShell]快速入门,基本语法,常用类型,函数,DotNet互操作'
date: 2021-03-24 15:42:12
tags:
  - powershell
  - 脚本语言
  - dotnet
categories:
  - dotnet
  - note
description: 'PowerShell 快速入门开始之前, 我们认定你已经有一定的编程基础, 熟悉 .NET 中的类型与对象.此文章对于 .NET 开发者来说更简单哦!在 PowerShell 中, 几乎一切都是对象. 与 CMD 有很大不同. PowerShell 是强类型的, 它基于 .NET, 故, PowerShell 可以近乎完美的调用 .NET 的标准库.0. 官方文档既然要学新东西, 肯定要会查阅官方文档才彳亍呀! 本文章参阅官方文档, 并使用更简单的语言讲述给读者, 在每一部分都会有推荐的官方文'
---


# PowerShell 快速入门


> 开始之前, 我们认定你已经有一定的编程基础, 熟悉 .NET 中的类型与对象.
>
> 此文章对于 .NET 开发者来说更简单哦!


在 PowerShell 中, 几乎一切都是对象. 与 CMD 有很大不同. PowerShell 是强类型的, 它基于 .NET, 故, PowerShell 可以近乎完美的调用 .NET 的标准库.


## 0. 准备工作


### - 官方文档


既然要学新东西, 肯定要会查阅官方文档才彳亍呀! 本文章参阅官方文档, 并使用更简单的语言讲述给读者, 在每一部分都会有推荐的官方文档链接目录, 点击即可跳转.


> [MSDocs PowerShell : 如何使用 PowerShell 官方文档](https://docs.microsoft.com/en-us/powershell/scripting/how-to-use-docs?view=powershell-7.1)
>
> [MSDocs PowerShell : PowerShell 是什么](https://docs.microsoft.com/en-us/powershell/scripting/overview?view=powershell-7.1)
>
> [MSDocs PowerShell : 关于主题 (涵盖有关 PowerShell 的一系列概念)](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_arrays?view=powershell-7.1)


另外, 在文档的中文页面的左侧索引部分, 你无法看到所有的内容, 例如 Reference 分类, 其中包含了非常多的 '隐藏' 内容, 只有你切换到英文页面才可以看到它们.


至于如何切换页面语言, 也简单, 只需要将地址栏中的 'zh-cn' 改为 'en-us' 即可, 同理, 如果某个页面你看不懂, 想要切换到中文, 可以将 'en-us' 改为 'zh-cn', 不过需要注意的是, 部分页面是没有提供中文翻译的.


为了方便, 本文涉及到的所有文档链接均为英文直链, 如需访问中文页面, 请自行修改链接地址.


### - 启动 PowerShell


PowerShell 同 CMD 一样有许多启动方式, 准备好开始了吗? 那么开始吧!


1. 按下 "Win" + "R" 打开 '运行' 窗口, 键入 'PowerShell' 并确认.

2. 按 "Win" + X, 然后按 'I', 或者按 'A'(以管理员身份运行).
3. 在桌面或资源管理器, 不选中任何条目, 按住 Shift, 右击背景, 在弹出的菜单中选择 '在此处启动 PowerShell'.
4. 在资源管理器中, 在地址栏输入 'PowerShell' 并按 Enter 键确认.


接下来, 出现的蓝底白字的控制台页面就是 PowerShell 的主程序了! 在其中输入指令(代码)可以运行一些东西.


## 1. 基本语法


### - 标准输出


让我们从一个 'hello world' 的输出开始吧, 与 CMD, Bash 一样, 使用 echo 指令是可以输出内容的.


```powershell
echo "Hello World!"
```


输出:


```txt
Hello World!
```


### - 获取帮助:


执行以下指令来获取关于 echo 的帮助内容.


```powershell
Get-Help echo
```


输出:


```txt
NAME
    Write-Output

SYNTAX
    Write-Output [-InputObject] <psobject[]> [-NoEnumerate]  [<CommonParameters>]


ALIASES
    write
    echo


REMARKS
    Get-Help cannot find the Help files for this cmdlet on this computer. It is displaying only partial help.
        -- To download and install Help files for the module that includes this cmdlet, use Update-Help.
        -- To view the Help topic for this cmdlet online, type: "Get-Help Write-Output -Online" or
           go to https://go.microsoft.com/fwlink/?LinkID=113427.     
```


看到结果, 你懂了吧, echo 实际上是 Write-Output 的别名, 同时也可以用 write 来执行.


另外, PowerShell 的指令是不区分大小写的, 所以 Write-Output 与 wRITE-oUTPUT 是一样执行效果的


### - 定义变量


> [PowerShell 官方文档 : 关于变量](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_variables)


定义变量的方式与 Bash 差不多, 都是用 $ 开头, 后跟变量名


```powershell
$var = 123
echo $var
```


输出:


```txt
123
```


### - 固定常量


PowerShell 中有一些常量, 不可被赋值 (如果对其赋值, 不会产生任何作用, 且不会抛出异常)


| 常量名 | 类型   | 值    |
| ------ | ------ | ----- |
| $null  | object | null  |
| $true  | bool   | true  |
| $false | bool   | false |


如果访问一些未定义的变量, 返回的值同样是 null


### - 类型概念


PowerShell 是强类型的. 例如刚刚我们定义的变量, 其实就是一个 int 变量. 示例:


```powershell
$a = 123
$b = 321
$c = $a + $b
echo $c
```


输出:


```txt
444
```


数字支持运算, 所以理所当然的可以进行运算.


### - 表达式与指令


刚刚我们所用的 echo 指令, 同时可以说是一个表达式. 而刚刚我们进行加法运算时, 所使用的 '$a + $b' 通用


我没猜错的话, 在刚刚的变量部分, 也同样有人这么执行:


```powershell
$a = 123
$b = 321
echo $a + $b
```


然后结果就是, 输出了


```txt
$a + $b
```


而解决办法就是, 为这个表达式加上括号, 优先运算这个表达式, 于是就正常了, 或者你可以直接 echo 一个表达式:


```powershell
echo (123+321)
```


输出


```txt
444
```


### - 运算符


刚刚我们从尝试了加法运算, 同时在 PowerShell 中, 也是支持减法, 乘法, 除法的, 除此之外还有小于, 大于, 等于, 大于等于, 小于等于:


```powershell
echo (1 -lt 10)
```


输出:


```txt
True
```


| 运算符 | 描述     |
| ------ | -------- |
| -eq    | 等于     |
| -ne    | 不等于   |
| -lt    | 小于     |
| -gt    | 大于     |
| -le    | 小于等于 |
| -ge    | 大于等于 |




## 2. 流程控制:


### - 基本判断


> [MSDocs PowerShell : 关于 If](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_if)


判断的话, 毋庸置疑是 if 语句. 当然, 无非是 'if' 'else' 'else if'


在 PowerShell 中, if 语句的语法可以是这样:


```powershell
if (exp) {
    statement block
} elseif {
    statement block
} else {
    statement block
}
# 示例 :
$randint = (New-Object Random).Next()    # 生成一个随机数, 并赋值给 randint 变量
if ($randint -eq 114514) {
    echo "嗯哼哼哼啊啊啊啊啊啊啊啊"
} elseif ($randint -gt 114514) {
    echo "吔屎了你, 梁非凡!"
} else {
    echo "听不见! 就这点声音还想开军舰!?"
}
```


没错, PowerShell 中, 'else if' 的话是不允许带空格的. 并且需要注意的是, 这个大括号不允许省略.


### - 循环语句


for 循环


> [MSDocs PowerShell : 关于 For](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_for?view=powershell-7.1)


```powershell
for (exp; exp; exp) {
    statement block
}
# 示例 :
for ($i = 0; $i -lt 10; $i++) {
    echo $i
}
for ($i = 1; $i -lt 10; $i++) {
    for ($j = 1; $j -le $i; $j++) {
        [Console]::Write(("{0}x{1}={2}`t" -f ($j, $i, $i * $j)))   # 不换行输出
    }
    [Console]::WriteLine()    # 换行
}
```


foreach 循环


> [MSDocs PowerShell : 关于 Foreach](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_foreach)


```powershell
foreach ($var in enumerable object) {
    statement block
}
# 示例 :
foreach ($num in @(1..5)) {
    echo $num
}
```


while 循环


> [MSDocs PowerShell : 关于 While](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_while)


```powershell
while (exp) {
    statement block
}
```


打破循环:


> [MSDocs PowerShell : 关于 Break](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_break)


```powershell
# 在 for 中打破循环 :
for ($i = 0; $i -lt 10; $i++) {
    if ($i -eq 5) {
        break
    }
    echo $i
}

# 在 foreach 语句中打破循环 :
foreach ($i in @(1..10)) {
    if ($i -eq 5) {
        break
    }
    echo $i
}

# 在 while 语句中打破循环 :
$i = 0
while ($true) {
    if ($i -eq 5) {
        break
    }
    echo $i
    $i++
}
```




## 3. 常用类型


### - 对象数组


> [MSDocs PowerShell : 关于数组](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_arrays?view=powershell-7.1)
>
> [MSDocs PowerShell : 关于数组的各项须知内容](https://docs.microsoft.com/en-us/powershell/scripting/learn/deep-dives/everything-about-arrays?view=powershell-7.1)


在 PowerShell 中, 当然也存在数组, 通过以下方式来创建:


```powershell
# 语法 1:
$var = @(element 1, element 2, element 3, ...., element n)
# 示例:
$array = @(1, 3, 4, 5)

# 语法 2:
$var = start..end
# 示例:
$array = @(1..5)    # 生成的数组等同于: @(1, 2, 3, 4, 5)

# 提示, @() 是可以省略的. 不过如果要创建空数组, 不可省略
```


数组的访问, 与其他大多数编程语言一致, 通过中括号就可以:


```powershell
echo @(1..4)[2]   # 输出 3
```


```powershell
$array = @(1..4)
$array[2] = 114514
echo $array[2]    # 输出 114514
```


PowerShell 的数组还支持获取多个值, 它返回一个新的数组:


```powershell
$array = @(1..4)
$array2 = $array[0, 2]
echo $array2[0]   # 输出 1
echo $array2[1]   # 输出 3
```


添加元素, 可以用 '+=' 运算符, 但注意, 不推荐这么做, 因为每为数组添加元素, 实际上都是重新创建了新的数组, 如果需要更灵活的数组, 请使用 泛型List


```powershell
$array = @(1..4)
$array += 114514
echo $array[4]    # 输出 114514
```


如果获取数组元素值时, 索引超出界限, 不会抛出异常, 会返回 null:


```powershell
$array = @(1..4)
echo $array[114514]  # 不会有任何输出, 因为值是null
```


如果对一个不是数组的变量使用索引操作符, 则会抛出异常:


```powershell
$test = $null
$test[0]          # 抛出异常
```


两个数组相加, 返回数组连接后的结果:


```powershell
$array1 = @(1..3)
$array2 = @(3..1)
echo ($array1 + $array2)
```


输出:


```txt
1
2
3
3
2
1
```


使用 -contains 操作符来判断数组是否包含某元素:


```powershell
$array = @(1..4)
echo ($array -contains 2)    # 输出 True
```


在 PowerShell 的数组中, -eq 操作符将对所有元素进行判断, 即, 遍历内容, 逐一进行 -eq 运算, 如若有任何元素运算返回 True, 则表达式返回真.


```powershell
$array = @(1..4)
echo ($array -eq 3)          # 返回 True
```


如果判断一个变量(可能是数组)为空, 请不要将这个变量放在左侧:


```powershell
$array -eq $null      # 不要这样使用
$null -eq $array      # 正确的使用方式
```


使用 Count 来获取一个数组的长度, 但注意, 如果一个变量值为null, 同样可以使用 count 来获取长度, 它返回 0:


```powershell
$array = @(1..4)
echo $array.Count     # 返回 4
echo $null.Count      # 返回 0
```


你也可以通过 Length 来获取一个数组的长度, 它与 Count 用法一样, 并且如果是 $null, 同样返回 0.


### - 哈希表


> [MSDocs PowerShell : 关于哈希表](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_hash_tables)
>
> [MSDocs PowerShell : 关于哈希表的一些注意事项](https://docs.microsoft.com/en-us/powershell/scripting/learn/deep-dives/everything-about-hashtable?view=powershell-7.1)


哈希表, 即 HashTable. 在 PowerShell 中这样创建 HashTable:


```powershell
$table = @{
    key = value
    key = value
}
# 示例 :
$table = @{}            # 创建空表
$table = @{             # 创建有初始值的表, 下面的示例将使用这个表
    "NullSlime" = 100
    "MoYuro" = 96
    "Muchen" = 94
    "KingHans" = 88
}
```


访问哈希表, 通过索引运算符, 指定键:


```powershell
# 使用刚刚创建的 table
echo $table["MoYuro"]    # 输出 96
$table["MoYuro"] = 97
echo $table["MoYuro"]    # 输出 97
```


向表中添加键值对, 使用 Add 方法:


```powershell
$table.Add(key, value)
# 示例 :
$table.Add("KunLong", 76)
```


删除表中的某个键值对, 使用 Remove 方法:


```powershell
$table.Remove(key)
# 示例 :
$table.Remove("KingHans")
```




哈希表和数组一样支持选择多个值, 只需要在索引运算符中指定多个键:


```powershell
$table[key1, key2, ... , key n]
$table[(key1, key2, ... , key n)]
$table[@(key1, key2, ... , key n)]
# 示例 :
$values = $table["NullSlime", "MoYuro"]    # 返回一个数组
echo $values[0]                            # 输出 100
echo $values[1]                            # 输出 96
```


通过 Keys 与 Values 来获取这个哈希表的所有键以及值


```powershell
$keys = $tables.Keys          # 返回哈希表的所有键
$values = $tables.Values      # 返回哈希表的所有值
```


可以通过对哈希表的枚举器来进行循环哈希表的每一个键值对:


```powershell
foreach ($pair in $table.GetEnumerator()) {
    echo ("{0}: {1}" -f ($pair.Key, $pair.Value))
}
```


当通过foreach对哈希表进行迭代时, 不可以对哈希表进行删减:


## 4. 脚本函数


### - 关于函数


> [PowerShell 官方文档 : 关于函数](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_functions)


PowerShell 中定义函数很简单, 它的本质是脚本块(ScriptBlock):


```powershell
function name(parameters) {
    statement block
}
# 示例 :
function myFunc($a, $b) {
    echo ($a + $b)
}
```


调用 PowerShell 中定义的函数, 语法如下:


```powershell
name argv1 argv2 argv3 .... argv n
# 示例 :
myFunc 114000 514    # 输出 114514
```


如果要获取这个函数的对象引用 可以这样:


```powershell
$function:name
# 示例 :
echo $function:myFunc
```


输出:


```txt
param($a, $b)

echo ($a + $b)
```


通过函数的对象引用, 可以使用 Invoke 来调用:


```powershell
$function:name.Invoke(parameters)
# 示例 :
$function:myFunc.Invoke(114000, 514)    # 输出 114514
```


删除一个一定义的函数, 可以使用 del 语句:


```powershell
del function:name
# 示例 :
del function:myFunc
echo $function:myFunc     # 由于函数已删除, 所以不会有任何输出
```


函数可以有返回值, 只需要使用 return 语句:


```powershell
function myFunc($a, $b) { return $a + $b; }
$result = myFunc 114000 514
echo $result      # 输出 114514
```


## 5. 面向对象


### - 使用对象


肯定会有人执行这个指令:


```powershell
echo Hello World!
```


虽然仅仅少了一对双引号, 但是, 它表示的意思其实跟之前我们写的完全不一样. 如果不加双引号, 那么在这个指令中, Hello 和 World! 会被认为是两个参数! 或者准确来说, 是两个对象!


这条没有加双引号的指令, 输出结果是:


```powershell
Hello
World!
```


另外, 命令的输出同样也是一个个对象, 执行下面的指令:


```powershell
1+1
```


输出


```txt
2
```


### - .NET 互操作


事实上, PowerShell 中的对象都是 .NET 对象. 并且我们可以进行 .NET 对象中所支持的操作. 执行下面的指令:


```powershell
(1+1).GetType()
```


输出:


```txt
IsPublic IsSerial Name                                     BaseType
-------- -------- ----                                     --------
True     True     Int32                                    System.ValueType
```


这熟悉的命名空间, 可不就是 .NET 吗?


试试以下指令:


```powershell
(echo 123).GetType()
```


输出:


```txt
IsPublic IsSerial Name                                     BaseType
-------- -------- ----                                     --------
True     True     Int32                                    System.ValueType
```


好家伙, 原来 echo 直接就是把这个对象输出出来对吧!


而事实上, 一个表达式的运算结果(返回值), 是会打印到控制台的, 这点与 Python 一样.


所以如果你执行一个 '1+1', 这个表达式的运算结果是 2, 则 2 会被打印到控制台.


```powershell
1+1  # 输出 2
```




如果你不希望一个语句的返回值被打印到控制台, 可以在首部添加 [void], 它表示将语句的返回值强制转换为 void 以保证没有输出


```powershell
[void](1+1)    # 没有输出
```


使用 New-Object 可以创建一个对象, 指定 .NET 对象的全名即可创建:


```powershell
$random = New-Object System.Random
$randint = $random.Next()    # 调用 System.Random 对象的 Next 方法
echo $randint                # 输出一个随机的数字
```


如果需要带参数的创建 .NET 对象, 直接在 New-Object 语句后添加参数数组即可:


```powershell
$str = New-Object String ('草', 20)    # 生草
echo $str  # 返回 '草草草草草草草草草草草草草草草草草草草草'
```


PowerShell 中的强制类型转换是这样使用的:


```powershell
[类型]对象
# 示例 :
$numArray = [int[]](1..4)   # 将 object[] 转换为 int[]
```


调用 .NET 类的静态方法, 使用下面的语法:


```powershell
[类的全名]::方法名(参数)
# 示例 :
[Console]::WriteLine("Hello world!")   # 输出 Hello world
```


访问字段, 也是一样的


```powershell
$ansi = [System.Text.Encoding]::Default
```


如果要使用某个命名空间, 使用下面的语句:


```powershell
using namespace System.Text
$ansi = [Encoding]::Default
```


值得高兴的是, 通过调用 .NET 的组件, 你甚至可以在 PowerShell 中创建 UI 窗体, 下面是一个简单的示例代码:


```powershell
Add-Type -AssemblyName System.Windows.Forms
Add-Type -AssemblyName System.Drawing

$form = New-Object System.Windows.Forms.Form
$form.Text = 'Select a Computer'
$form.Size = New-Object System.Drawing.Size(300,200)
$form.StartPosition = 'CenterScreen'

$okButton = New-Object System.Windows.Forms.Button
$okButton.Anchor = [System.Windows.Forms.AnchorStyles]::Bottom -bor [System.Windows.Forms.AnchorStyles]::Right
$okButton.Location = New-Object System.Drawing.Point(75,120)
$okButton.Size = New-Object System.Drawing.Size(75,23)
$okButton.Text = 'OK'
$okButton.DialogResult = [System.Windows.Forms.DialogResult]::OK
$form.AcceptButton = $okButton
$form.Controls.Add($okButton)

$cancelButton = New-Object System.Windows.Forms.Button
$cancelButton.Anchor = [System.Windows.Forms.AnchorStyles]::Bottom -bor [System.Windows.Forms.AnchorStyles]::Right
$cancelButton.Location = New-Object System.Drawing.Point(150,120)
$cancelButton.Size = New-Object System.Drawing.Size(75,23)
$cancelButton.Text = 'Cancel'
$cancelButton.DialogResult = [System.Windows.Forms.DialogResult]::Cancel
$form.CancelButton = $cancelButton
$form.Controls.Add($cancelButton)

$label = New-Object System.Windows.Forms.Label
$label.Anchor = [System.Windows.Forms.AnchorStyles]::Top -bor [System.Windows.Forms.AnchorStyles]::Left
$label.Location = New-Object System.Drawing.Point(10,20)
$label.Size = New-Object System.Drawing.Size(280,20)
$label.Text = 'Please select a computer:'
$form.Controls.Add($label)

$listBox = New-Object System.Windows.Forms.ListBox
$listBox.Anchor = [System.Windows.Forms.AnchorStyles]::Top -bor [System.Windows.Forms.AnchorStyles]::Left -bor [System.Windows.Forms.AnchorStyles]::Bottom -bor [System.Windows.Forms.AnchorStyles]::Right
$listBox.Location = New-Object System.Drawing.Point(10,40)
$listBox.Size = New-Object System.Drawing.Size(260,20)
$listBox.Height = 80

[void] $listBox.Items.Add('atl-dc-001')
[void] $listBox.Items.Add('atl-dc-002')
[void] $listBox.Items.Add('atl-dc-003')
[void] $listBox.Items.Add('atl-dc-004')
[void] $listBox.Items.Add('atl-dc-005')
[void] $listBox.Items.Add('atl-dc-006')
[void] $listBox.Items.Add('atl-dc-007')

$form.Controls.Add($listBox)

$form.Topmost = $true

$result = $form.ShowDialog()

if ($result -eq [System.Windows.Forms.DialogResult]::OK)
{
    $x = $listBox.SelectedItem
    [System.Windows.Forms.MessageBox]::Show("你选择了:" + $x.ToString())
} else {
    [System.Windows.Forms.MessageBox]::Show("你没有选择任何条目")
}

```


