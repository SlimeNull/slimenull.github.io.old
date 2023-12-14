---
title: '[C#] 运算包含数学表达式的字符串'
slug: '[CSharp]运算包含数学表达式的字符串'
date: 2020-10-29 11:41:22
tags:
  - 算法
  - .net
  - c#
categories:
  - 算法
  - .NET
  - 类库
description: '关于:原理讲解代码示例完整程序源码下载适用于:实例代码适用于: .NET Framework & .NET Core算法通用. 只要你能够找到与算法对应的实现方式.将要实现:分析表达式实现所有的通用运算符实现三元运算符原理:使用"状态机"算法分析表达式根据运算符优先级, 不断尝试运算, 最终得到结果详解:第一步, 我们需要将运算表达式分成一个个节点(token), 这个节点可能是一个数字, 可能是一个运算符, 至于表达式里的括号, 我们会使用递归来'
---

## 关于:

0. ###### 原理讲解
1. 代码示例
2. 完整程序源码下载

## 适用于:

0. 实例代码适用于: .NET Framework & .NET Core
1. 算法通用. 只要你能够找到与算法对应的实现方式.

## 将要实现:

0. 分析表达式
1. 实现所有的通用运算符
2. 实现三元运算符
3. 支持嵌套的括号

## 原理:

0. 使用"状态机"算法分析表达式
1. 根据运算符优先级, 不断尝试运算, 最终得到结果

## 详解:

0. 第一步, 我们需要将运算表达式分成一个个节点(token), 这个节点可能是一个数字, 可能是一个运算符, 至于表达式里的括号, 我们会使用递归来进行运算. 我们使用下面的class来表示这个token.

```csharp
enum CalcTokenType
{
    // 规定枚举所对应的int为2的倍数是为了后面方便识别
    None = 0, Number = 1, Operator = 2
}

enum CalcOperator
{
    None,
    Add = 1,       // +
    Sub = 2,       // -
    Mul = 4,       // *
    Div = 8,       // /
    Mod = 16,      // %
    Pow = 32,      // **
    Lss = 64,      // <
    Gtr = 128,     // >
    Leq = 256,     // <=
    Geq = 512,     // >=
    Equ = 1024,    // =
    Neq = 2048,    // !=
    Not = 4096,    // !
    And = 8192,    // &
    Or = 16384,    // |
    If = 32768,    // 三元表达式中的 '?' 符号
    Witch = 65536  // 三元表达式中的 ':' 符号
}

class CalcToken
{
    public CalcToken(CalcTokenType type, object content)
    {
        this.Type = type;
        this.Content = content;
    }
    CalcTokenType Type;
    object Content;
    // Content, 当Type是None时, 值应该是null
    // 当Type是Number时, 值应该是double
    // 当Type是Operator时, 值应该是CalcOperator枚举变量
}
```

1. 继续, 那么, 先是如何分析一个简单的表达式, 例如下面的:

> 11+1

2. 分析这个表达式, 我们首先定义一些字符串常量, 如下:


```csharp
char[] OperatorChars = "+-*/<>!=&|?:".ToArray();  // 表示操作符的字符
char[] NumberChars = "0123456789.".ToArray();     // 表示数字的字符
char[] EmptyChars = " \t".ToArray();          // 表示空字符
```

3. 我们将逐字符对其分析(有限状态机): 使用List&lt;CalcToken&gt; tokens来保存分析出的token

> 1. 当开始时, 我们取到一个字符'1', 现在我们还不是在分析任何类型, 但是经过定义的字符集常量的Contains判断, 我们发现它是属于数字的, 那么我们知道, 现在我们正在分析数字.
> 2. 存储下来这个字符'1', 用什么, 你应该知道吧? 要么是List<char>, 要么是StringBuilder. 并且, 记录下我们现在的状态, 我们分析到了数字
> 3. 到分析到第二个数字时, 我们当前的状态是正在分析数字, 那么在这个状态下, 如果我们又取到一个数字, 那么我们需要存储下这个数字, 并且当前状态不变, 如果我们取到一个操作符字符, 那么代表现在这个数字结束了, 使用double.Parse将已保存的字符串(一个或多个由数字字符组成的), 转换为double类型, 然后存储下这个token, 并且清空存储的字符, 将当前取到的字符存进去, 然后改变当前状态到"正在分析操作符".
> 4. 我们的表达式是"11+1", 第二个字符是一个数字字符, 也就是说, 我们应存下这个字符, 且状态不变. 于是, 我们已经存储下了两个字符{'1', '1'}, 当前状态是数字
> 5. 到第三个字符了, 我们发现它不是一个数字字符, 而是一个操作符字符, 于是, 将已保存的字符串转换为double, 并添加到tokens, 当前状态是:正在分析操作符
> 6. 到第四个字符, 它又不是一个操作符字符了, 它现在是一个数字, 所以, 将已存储的操作符字符保存
> 7. 到最后, 分析结束, 但临时存储的部分还有一个字符'1', 将其转换为double, 然后存储到tokens, 于是, 我们成功完成了对表达式的分词, 至此, 基本原理已经讲清, 尝试理解下面的函数


```csharp
// 下面是分词核心, 这里面包含对括号的分析
// bool inner表示这个函数是否是被另一个分词函数调用的, 也就是说是否在递归状态, 如果是, 则表示在分析一个括号内的表达式, 碰到')'的时候立即return;
bool TryParseCalcTokens(ref char[] source, ref int offset, out List<CalcToken> result, bool inner = false, int level = 0)
{
    result = new List<CalcToken>();

    for (; offset < source.Length; offset++)
    {
        if (EmptyChars.Contains(source[offset]))
        {
            continue;
        }
        else if (source[offset] == '(')
        {
            offset++;
            if (TryParseCalcTokens(ref source, ref offset, out List<CalcToken> newrst, true, level + 1))
            {
                result.Add(new CalcToken(CalcTokenType.Expression, newrst));
            }
            else
            {
                errors.Add($"Error when parsing inner expression. Level:{level};");
                return false;
            }
        }
        else if (source[offset] == ')')
        {
            return true;
        }
        else
        {
            if (TryParseToken(ref source, ref offset, out CalcToken newcctk))
            {
                result.Add(newcctk);   // 这里是将分析好的token添加到tokens
            }
            else
            {
                errors.Add($"Error when parsing expression token. Level:{level};");
                return false;
            }
        }
    }


    return true;
}
```


4. 开始运算表达式: (最简单的方法)

> 遍历我们的tokens, 第一次遍历, 查找优先级最高的操作符并运算结果, 第二次遍历查找优先级略次的操作符并运算结果, 如此往复, 查找完所有的表达式

```csharp
// 下面是运算核心
bool TryCalculateTokens(ref List<CalcToken> source, out double result, out List<string> errors)
{
    errors = new List<string>();
    result = 0;

    for (int i = 0; i < source.Count; i++)
    {
        if (source[i].Type == CalcTokenType.Operator && CheckOperator(CalcOperator.Mul | CalcOperator.Div | CalcOperator.Mod | CalcOperator.Pow, (CalcOperator)source[i].Content) && CheckType(ref source, CalcTokenType.Number | CalcTokenType.Expression, i, -1) && CheckType(ref source, CalcTokenType.Number | CalcTokenType.Expression, i, 1))
        {
            if (TryGetTokenValue(ref errors, source[i - 1], out double leftnum) && TryGetTokenValue(ref errors, source[i + 1], out double rightnum))
            {
                double tmpnum = 0;
                switch ((CalcOperator)source[i].Content)
                {
                    case CalcOperator.Mul:
                        tmpnum = leftnum * rightnum;
                        break;
                    case CalcOperator.Div:
                        tmpnum = leftnum / rightnum;
                        break;
                    case CalcOperator.Mod:
                        tmpnum = leftnum % rightnum;
                        break;
                    case CalcOperator.Pow:
                        tmpnum = Math.Pow(leftnum, rightnum);
                        break;
                    default:
                        throw new Exception();
                }
                source[i - 1] = new CalcToken(CalcTokenType.Number, tmpnum);
                source.RemoveRange(i, 2);
                i--;
            }
            else
            {
                errors.Add($"Cannot get the value of token when calculate '{source[i].Content}';");
                return false;
            }
        }
    }    // * / % **
    for (int i = 0; i < source.Count; i++)
    {
        if (source[i].Type == CalcTokenType.Operator && CheckOperator(CalcOperator.Add | CalcOperator.Sub, (CalcOperator)source[i].Content) && CheckType(ref source, CalcTokenType.Number | CalcTokenType.Expression, i, 1))
        {
            if (CheckType(ref source, CalcTokenType.Number | CalcTokenType.Expression, i, -1))
            {
                if (TryGetTokenValue(ref errors, source[i - 1], out double leftnum) && TryGetTokenValue(ref errors, source[i + 1], out double rightnum))
                {
                    double tmpnum = 0;
                    switch ((CalcOperator)source[i].Content)
                    {
                        case CalcOperator.Add:
                            tmpnum = leftnum + rightnum;
                            break;
                        case CalcOperator.Sub:
                            tmpnum = leftnum - rightnum;
                            break;
                        default:
                            throw new Exception();
                    }
                    source[i - 1] = new CalcToken(CalcTokenType.Number, tmpnum);
                    source.RemoveRange(i, 2);
                    i--;
                }
                else
                {
                    errors.Add($"Cannot get the value of token when calculate '{source[i].Content}';");
                    return false;
                }
            }
            else
            {
                if (TryGetTokenValue(ref errors, source[i + 1], out double rightnum))
                {
                    double tmpnum = 0;
                    switch ((CalcOperator)source[i].Content)
                    {
                        case CalcOperator.Add:
                            tmpnum = rightnum;
                            break;
                        case CalcOperator.Sub:
                            tmpnum = 0 - rightnum;
                            break;
                        default:
                            throw new Exception();
                    }
                    source[i] = new CalcToken(CalcTokenType.Number, tmpnum);
                    source.RemoveRange(i + 1, 1);
                }
                else
                {
                    errors.Add($"Cannot get the value of token when calculate '{source[i].Content}';");
                    return false;
                }
            }
        }
    }    // + -
    for (int i = 0; i < source.Count; i++)
    {
        if (source[i].Type == CalcTokenType.Operator && CheckOperator(CalcOperator.Gtr | CalcOperator.Lss | CalcOperator.Geq | CalcOperator.Leq, (CalcOperator)source[i].Content) && CheckType(ref source, CalcTokenType.Number | CalcTokenType.Expression, i, -1) && CheckType(ref source, CalcTokenType.Number | CalcTokenType.Expression, i, 1))
        {
            if (TryGetTokenValue(ref errors, source[i - 1], out double leftnum) && TryGetTokenValue(ref errors, source[i + 1], out double rightnum))
            {
                double tmpnum = 0;
                switch ((CalcOperator)source[i].Content)
                {
                    case CalcOperator.Gtr:
                        tmpnum = leftnum > rightnum ? 1 : 0;
                        break;
                    case CalcOperator.Lss:
                        tmpnum = leftnum < rightnum ? 1 : 0;
                        break;
                    case CalcOperator.Geq:
                        tmpnum = leftnum >= rightnum ? 1 : 0;
                        break;
                    case CalcOperator.Leq:
                        tmpnum = leftnum <= rightnum ? 1 : 0;
                        break;
                    default:
                        throw new Exception();
                }
                source[i - 1] = new CalcToken(CalcTokenType.Number, tmpnum);
                source.RemoveRange(i, 2);
                i--;
            }
            else
            {
                errors.Add($"Cannot get the value of token when calculate '{source[i].Content}';");
                return false;
            }
        }
    }    // > < >= <=
    for (int i = 0; i < source.Count; i++)
    {
        if (source[i].Type == CalcTokenType.Operator && CheckOperator(CalcOperator.Equ | CalcOperator.Neq, (CalcOperator)source[i].Content) && CheckType(ref source, CalcTokenType.Number | CalcTokenType.Expression, i, -1) && CheckType(ref source, CalcTokenType.Number | CalcTokenType.Expression, i, 1))
        {
            if (TryGetTokenValue(ref errors, source[i - 1], out double leftnum) && TryGetTokenValue(ref errors, source[i + 1], out double rightnum))
            {
                double tmpnum = 0;
                switch ((CalcOperator)source[i].Content)
                {
                    case CalcOperator.Equ:
                        tmpnum = leftnum == rightnum ? 1 : 0;
                        break;
                    case CalcOperator.Neq:
                        tmpnum = leftnum != rightnum ? 1 : 0;
                        break;
                    default:
                        throw new Exception();
                }
                source[i - 1] = new CalcToken(CalcTokenType.Number, tmpnum);
                source.RemoveRange(i, 2);
                i--;
            }
            else
            {
                errors.Add($"Cannot get the value of token when calculate '{source[i].Content}';");
                return false;
            }
        }
    }    // == !=
    for (int i = 0; i < source.Count; i++)
    {
        if (source[i].Type == CalcTokenType.Operator && CheckOperator(CalcOperator.Add | CalcOperator.Or, (CalcOperator)source[i].Content) && CheckType(ref source, CalcTokenType.Number | CalcTokenType.Expression, i, -1) && CheckType(ref source, CalcTokenType.Number | CalcTokenType.Expression, i, 1))
        {
            if (TryGetTokenValue(ref errors, source[i - 1], out double leftnum) && TryGetTokenValue(ref errors, source[i + 1], out double rightnum))
            {
                double tmpnum = 0;
                switch ((CalcOperator)source[i].Content)
                {
                    case CalcOperator.Add:
                        tmpnum = leftnum != 0 && rightnum != 0 ? 1 : 0;
                        break;
                    case CalcOperator.Or:
                        tmpnum = leftnum != 0 || rightnum != 0 ? 1 : 0;
                        break;
                    default:
                        throw new Exception();
                }
                source[i - 1] = new CalcToken(CalcTokenType.Number, tmpnum);
                source.RemoveRange(i, 2);
                i--;
            }
            else
            {
                errors.Add($"Cannot get the value of token when calculate '{source[i].Content}';");
                return false;
            }
        }
    }    // & |
    for (int i = 0; i < source.Count; i++)
    {
        if (source[i].Type == CalcTokenType.Operator && CheckOperator(CalcOperator.If, (CalcOperator)source[i].Content) && CheckType(ref source, CalcTokenType.Number | CalcTokenType.Expression, i, -1) && CheckType(ref source, CalcTokenType.Number | CalcTokenType.Expression, i, 1) && CheckType(ref source, CalcTokenType.Operator, i, 2) && CheckType(ref source, CalcTokenType.Number | CalcTokenType.Expression, i, 3) && CheckOperator(CalcOperator.Witch, (CalcOperator)source[i + 2].Content))
        {
            if (TryGetTokenValue(ref errors, source[i - 1], out double basic))
            {
                double tmpnum = 0;
                if (basic != 0)
                {
                    if (TryGetTokenValue(ref errors, source[i + 1], out double leftnum))
                    {
                        tmpnum = leftnum;
                    }
                    else
                    {
                        errors.Add($"Cannot get the value of token when calculate '{source[i].Content}';");
                        return false;
                    }
                }
                else
                {
                    if (TryGetTokenValue(ref errors, source[i + 3], out double rightnum))
                    {
                        tmpnum = rightnum;
                    }
                    else
                    {
                        errors.Add($"Cannot get the value of token when calculate '{source[i].Content}';");
                        return false;
                    }
                }
                source[i - 1] = new CalcToken(CalcTokenType.Number, tmpnum);
                source.RemoveRange(i, 4);
                i--;
            }
            else
            {
                errors.Add($"Cannot get the value of token when calculate '{source[i].Content}';");
                return false;
            }
        }
    }    // 三元表达式

    if (source.Count == 1 && source[0].Type == CalcTokenType.Number)
    {
        result = (double)source[0].Content;
        return true;
    }
    else
    {
        errors.Add($"Final result after calculate is not a single number. '{string.Join(" ", source)}';");
        return false;
    }
}

```


<br /><br /><br /><br /><br /><br /><br />

- 完整源代码:<a href="https://github.com/SlimeNull/CalculatorPlus">Github仓库</a>
  最新版本已经使用了逆波兰表达式
