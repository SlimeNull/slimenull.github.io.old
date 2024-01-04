---
title: '[C#] 计算字符串在控制台上显示的宽度, 包含所有Char能表示的字符!'
slug: '[CSharp]计算字符串在控制台上显示的宽度,包含所有Char能表示的字符,'
date: 2020-10-28T23:27:11+08:00
tags:
  - 字符串
categories:
  - dotnet
  - 类库
  - 控制台
description: '通过运算, 获取字符串在控制台上现实的宽度(单位为一个英文字母的宽度, 高度为控制台中一行的高度)在网上找了半天, 唯一一个正儿八经的, 就是通过GBK编码的字节数来推测所占宽度, 但我认为这个有点不大妥当, 例如某些特殊字符, 可能就不被GBK编码包含.所以, 我这里提供了一个可获取字符串显示宽度的可行方法.首先, 我通过循环C#中所有的字符, 并将其打印到控制台上, 运算单个字符所占宽度, 最终得出了一个List<int>, 通过这个列表, 只需要以字符强制转换为int的.'
---

- 通过运算, 获取字符串在控制台上现实的宽度(单位为一个英文字母的宽度, 高度为控制台中一行的高度)

> - 在网上找了半天, 唯一一个正儿八经的, 就是通过GBK编码的字节数来推测所占宽度, 但我认为这个有点不大妥当, 例如某些特殊字符, 可能就不被GBK编码包含.
> - 所以, 我这里提供了一个可获取字符串显示宽度的可行方法.
> **Github 仓库地址: [Null.ConsoleEx](https://github.com/SlimeNull/Null.ConsoleEx)** ==重点==


### 实现过程:


1. 首先, 我通过循环C#中所有的字符, 并将其打印到控制台上, 运算单个字符所占宽度, 最终得出了一个List&lt;int&gt;, 通过这个列表, 只需要以字符强制转换为int的值作为索引, 访问对应的元素, 即可得出字符对应宽度

```csharp
int GetCharLen(char c)
{
    Console.CursorLeft = Console.BufferWidth / 2 - 1;
    int start = Console.CursorLeft;
    Console.Write(c);
    return Console.CursorLeft - start;
}
```

2. 之后, 再获取区间, 稍加处理, 即得到可获取char所有字符在控制台中显示的宽度.

```csharp
public static int GetCharDisplayLength(char c)
{
    if (index < 7)
        return 1;
    else if (index < 9)
        return 0;
    else if (index < 10)
        return 8;
    else if (index < 11)
        return 0;
    else if (index < 13)
        return 1;
    else if (index < 14)
        return 0;
    else if (index < 162)
        return 1;
    else if (index < 166)
        return 2;
    else if (index < 167)
        return 1;
    else if (index < 169)
        return 2;
    else if (index < 175)
        return 1;
    else if (index < 178)
        return 2;
    else if (index < 180)
        return 1;
    else if (index < 182)
        return 2;
    else if (index < 183)
        return 1;
    else if (index < 184)
        return 2;
    else if (index < 215)
        return 1;
    else if (index < 216)
        return 2;
    else if (index < 247)
        return 1;
    else if (index < 248)
        return 2;
    else if (index < 449)
        return 1;
    else if (index < 450)
        return 2;
    else if (index < 711)
        return 1;
    else if (index < 712)
        return 2;
    else if (index < 713)
        return 1;
    else if (index < 716)
        return 2;
    else if (index < 729)
        return 1;
    else if (index < 730)
        return 2;
    else if (index < 913)
        return 1;
    else if (index < 930)
        return 2;
    else if (index < 931)
        return 1;
    else if (index < 938)
        return 2;
    else if (index < 945)
        return 1;
    else if (index < 962)
        return 2;
    else if (index < 963)
        return 1;
    else if (index < 970)
        return 2;
    else if (index < 1025)
        return 1;
    else if (index < 1026)
        return 2;
    else if (index < 1040)
        return 1;
    else if (index < 1104)
        return 2;
    else if (index < 1105)
        return 1;
    else if (index < 1106)
        return 2;
    else if (index < 8208)
        return 1;
    else if (index < 8209)
        return 2;
    else if (index < 8211)
        return 1;
    else if (index < 8215)
        return 2;
    else if (index < 8216)
        return 1;
    else if (index < 8218)
        return 2;
    else if (index < 8220)
        return 1;
    else if (index < 8222)
        return 2;
    else if (index < 8229)
        return 1;
    else if (index < 8231)
        return 2;
    else if (index < 8240)
        return 1;
    else if (index < 8241)
        return 2;
    else if (index < 8242)
        return 1;
    else if (index < 8244)
        return 2;
    else if (index < 8245)
        return 1;
    else if (index < 8246)
        return 2;
    else if (index < 8251)
        return 1;
    else if (index < 8252)
        return 2;
    else if (index < 8254)
        return 1;
    else if (index < 8255)
        return 2;
    else if (index < 8364)
        return 1;
    else if (index < 8365)
        return 2;
    else if (index < 8451)
        return 1;
    else if (index < 8452)
        return 2;
    else if (index < 8453)
        return 1;
    else if (index < 8454)
        return 2;
    else if (index < 8457)
        return 1;
    else if (index < 8458)
        return 2;
    else if (index < 8470)
        return 1;
    else if (index < 8471)
        return 2;
    else if (index < 8481)
        return 1;
    else if (index < 8482)
        return 2;
    else if (index < 8544)
        return 1;
    else if (index < 8556)
        return 2;
    else if (index < 8560)
        return 1;
    else if (index < 8570)
        return 2;
    else if (index < 8592)
        return 1;
    else if (index < 8596)
        return 2;
    else if (index < 8598)
        return 1;
    else if (index < 8602)
        return 2;
    else if (index < 8712)
        return 1;
    else if (index < 8713)
        return 2;
    else if (index < 8719)
        return 1;
    else if (index < 8720)
        return 2;
    else if (index < 8721)
        return 1;
    else if (index < 8722)
        return 2;
    else if (index < 8725)
        return 1;
    else if (index < 8726)
        return 2;
    else if (index < 8728)
        return 1;
    else if (index < 8729)
        return 2;
    else if (index < 8730)
        return 1;
    else if (index < 8731)
        return 2;
    else if (index < 8733)
        return 1;
    else if (index < 8737)
        return 2;
    else if (index < 8739)
        return 1;
    else if (index < 8740)
        return 2;
    else if (index < 8741)
        return 1;
    else if (index < 8742)
        return 2;
    else if (index < 8743)
        return 1;
    else if (index < 8748)
        return 2;
    else if (index < 8750)
        return 1;
    else if (index < 8751)
        return 2;
    else if (index < 8756)
        return 1;
    else if (index < 8760)
        return 2;
    else if (index < 8764)
        return 1;
    else if (index < 8766)
        return 2;
    else if (index < 8776)
        return 1;
    else if (index < 8777)
        return 2;
    else if (index < 8780)
        return 1;
    else if (index < 8781)
        return 2;
    else if (index < 8786)
        return 1;
    else if (index < 8787)
        return 2;
    else if (index < 8800)
        return 1;
    else if (index < 8802)
        return 2;
    else if (index < 8804)
        return 1;
    else if (index < 8808)
        return 2;
    else if (index < 8814)
        return 1;
    else if (index < 8816)
        return 2;
    else if (index < 8853)
        return 1;
    else if (index < 8854)
        return 2;
    else if (index < 8857)
        return 1;
    else if (index < 8858)
        return 2;
    else if (index < 8869)
        return 1;
    else if (index < 8870)
        return 2;
    else if (index < 8895)
        return 1;
    else if (index < 8896)
        return 2;
    else if (index < 8978)
        return 1;
    else if (index < 8979)
        return 2;
    else if (index < 9312)
        return 1;
    else if (index < 9322)
        return 2;
    else if (index < 9332)
        return 1;
    else if (index < 9372)
        return 2;
    else if (index < 9632)
        return 1;
    else if (index < 9634)
        return 2;
    else if (index < 9650)
        return 1;
    else if (index < 9652)
        return 2;
    else if (index < 9660)
        return 1;
    else if (index < 9662)
        return 2;
    else if (index < 9670)
        return 1;
    else if (index < 9672)
        return 2;
    else if (index < 9675)
        return 1;
    else if (index < 9676)
        return 2;
    else if (index < 9678)
        return 1;
    else if (index < 9680)
        return 2;
    else if (index < 9698)
        return 1;
    else if (index < 9702)
        return 2;
    else if (index < 9733)
        return 1;
    else if (index < 9735)
        return 2;
    else if (index < 9737)
        return 1;
    else if (index < 9738)
        return 2;
    else if (index < 9792)
        return 1;
    else if (index < 9793)
        return 2;
    else if (index < 9794)
        return 1;
    else if (index < 9795)
        return 2;
    else if (index < 12288)
        return 1;
    else if (index < 12292)
        return 2;
    else if (index < 12293)
        return 1;
    else if (index < 12312)
        return 2;
    else if (index < 12317)
        return 1;
    else if (index < 12319)
        return 2;
    else if (index < 12321)
        return 1;
    else if (index < 12330)
        return 2;
    else if (index < 12353)
        return 1;
    else if (index < 12436)
        return 2;
    else if (index < 12443)
        return 1;
    else if (index < 12447)
        return 2;
    else if (index < 12449)
        return 1;
    else if (index < 12535)
        return 2;
    else if (index < 12540)
        return 1;
    else if (index < 12543)
        return 2;
    else if (index < 12549)
        return 1;
    else if (index < 12586)
        return 2;
    else if (index < 12690)
        return 1;
    else if (index < 12704)
        return 2;
    else if (index < 12832)
        return 1;
    else if (index < 12868)
        return 2;
    else if (index < 12928)
        return 1;
    else if (index < 12958)
        return 2;
    else if (index < 12959)
        return 1;
    else if (index < 12964)
        return 2;
    else if (index < 12969)
        return 1;
    else if (index < 12977)
        return 2;
    else if (index < 13198)
        return 1;
    else if (index < 13200)
        return 2;
    else if (index < 13212)
        return 1;
    else if (index < 13215)
        return 2;
    else if (index < 13217)
        return 1;
    else if (index < 13218)
        return 2;
    else if (index < 13252)
        return 1;
    else if (index < 13253)
        return 2;
    else if (index < 13262)
        return 1;
    else if (index < 13263)
        return 2;
    else if (index < 13265)
        return 1;
    else if (index < 13267)
        return 2;
    else if (index < 13269)
        return 1;
    else if (index < 13270)
        return 2;
    else if (index < 19968)
        return 1;
    else if (index < 40870)
        return 2;
    else if (index < 55296)
        return 1;
    else if (index < 55297)
        return 0;
    else if (index < 56320)
        return 1;
    else if (index < 56321)
        return 2;
    else if (index < 57344)
        return 1;
    else if (index < 59335)
        return 2;
    else if (index < 59337)
        return 1;
    else if (index < 59493)
        return 2;
    else if (index < 63733)
        return 1;
    else if (index < 63734)
        return 2;
    else if (index < 63744)
        return 1;
    else if (index < 64046)
        return 2;
    else if (index < 65072)
        return 1;
    else if (index < 65074)
        return 2;
    else if (index < 65075)
        return 1;
    else if (index < 65093)
        return 2;
    else if (index < 65097)
        return 1;
    else if (index < 65107)
        return 2;
    else if (index < 65108)
        return 1;
    else if (index < 65112)
        return 2;
    else if (index < 65113)
        return 1;
    else if (index < 65127)
        return 2;
    else if (index < 65128)
        return 1;
    else if (index < 65132)
        return 2;
    else if (index < 65281)
        return 1;
    else if (index < 65375)
        return 2;
    else if (index < 65504)
        return 1;
    else if (index < 65510)
        return 2;
    else if (index < 65536)
        return 1;
    else
        return 0;
}

```

再稍加处理, 使得每一个字符所需进行的判断次数差异减少. (类似于二叉树), 
**下面这个代码直接就能用哦~ 而且是包含Char能表示的所有字符的.**

```csharp
public static int GetCharDisplayLength(char c)
{
    int index = c;
    if (index < 8800)
    {
        if (index < 8246)
        {
            if (index < 913)
            {
                if (index < 183)
                {
                    if (index < 166)
                    {
                        if (index < 11)
                        {
                            if (index < 9)
                            {
                                if (index < 7)
                                    return 1;
                                else
                                    return 0;
                            }
                            else
                            {
                                if (index < 10)
                                    return 8;
                                else
                                    return 0;
                            }
                        }
                        else
                        {
                            if (index < 14)
                            {
                                if (index < 13)
                                    return 1;
                                else
                                    return 0;
                            }
                            else
                            {
                                if (index < 162)
                                    return 1;
                                else
                                    return 2;
                            }
                        }
                    }
                    else
                    {
                        if (index < 178)
                        {
                            if (index < 169)
                            {
                                if (index < 167)
                                    return 1;
                                else
                                    return 2;
                            }
                            else
                            {
                                if (index < 175)
                                    return 1;
                                else
                                    return 2;
                            }
                        }
                        else
                        {
                            if (index < 182)
                            {
                                if (index < 180)
                                    return 1;
                                else
                                    return 2;
                            }
                            else
                            {
                                return 1;
                            }
                        }
                    }
                }
                else
                {
                    if (index < 450)
                    {
                        if (index < 247)
                        {
                            if (index < 215)
                            {
                                if (index < 184)
                                    return 2;
                                else
                                    return 1;
                            }
                            else
                            {
                                if (index < 216)
                                    return 2;
                                else
                                    return 1;
                            }
                        }
                        else
                        {
                            if (index < 449)
                            {
                                if (index < 248)
                                    return 2;
                                else
                                    return 1;
                            }
                            else
                            {
                                return 2;
                            }
                        }
                    }
                    else
                    {
                        if (index < 716)
                        {
                            if (index < 712)
                            {
                                if (index < 711)
                                    return 1;
                                else
                                    return 2;
                            }
                            else
                            {
                                if (index < 713)
                                    return 1;
                                else
                                    return 2;
                            }
                        }
                        else
                        {
                            if (index < 730)
                            {
                                if (index < 729)
                                    return 1;
                                else
                                    return 2;
                            }
                            else
                            {
                                return 1;
                            }
                        }
                    }
                }
            }
            else
            {
                if (index < 8209)
                {
                    if (index < 1025)
                    {
                        if (index < 945)
                        {
                            if (index < 931)
                            {
                                if (index < 930)
                                    return 2;
                                else
                                    return 1;
                            }
                            else
                            {
                                if (index < 938)
                                    return 2;
                                else
                                    return 1;
                            }
                        }
                        else
                        {
                            if (index < 963)
                            {
                                if (index < 962)
                                    return 2;
                                else
                                    return 1;
                            }
                            else
                            {
                                if (index < 970)
                                    return 2;
                                else
                                    return 1;
                            }
                        }
                    }
                    else
                    {
                        if (index < 1105)
                        {
                            if (index < 1040)
                            {
                                if (index < 1026)
                                    return 2;
                                else
                                    return 1;
                            }
                            else
                            {
                                if (index < 1104)
                                    return 2;
                                else
                                    return 1;
                            }
                        }
                        else
                        {
                            if (index < 8208)
                            {
                                if (index < 1106)
                                    return 2;
                                else
                                    return 1;
                            }
                            else
                            {
                                return 2;
                            }
                        }
                    }
                }
                else
                {
                    if (index < 8229)
                    {
                        if (index < 8218)
                        {
                            if (index < 8215)
                            {
                                if (index < 8211)
                                    return 1;
                                else
                                    return 2;
                            }
                            else
                            {
                                if (index < 8216)
                                    return 1;
                                else
                                    return 2;
                            }
                        }
                        else
                        {
                            if (index < 8222)
                            {
                                if (index < 8220)
                                    return 1;
                                else
                                    return 2;
                            }
                            else
                            {
                                return 1;
                            }
                        }
                    }
                    else
                    {
                        if (index < 8242)
                        {
                            if (index < 8240)
                            {
                                if (index < 8231)
                                    return 2;
                                else
                                    return 1;
                            }
                            else
                            {
                                if (index < 8241)
                                    return 2;
                                else
                                    return 1;
                            }
                        }
                        else
                        {
                            if (index < 8245)
                            {
                                if (index < 8244)
                                    return 2;
                                else
                                    return 1;
                            }
                            else
                            {
                                return 2;
                            }
                        }
                    }
                }
            }
        }
        else
        {
            if (index < 8721)
            {
                if (index < 8481)
                {
                    if (index < 8452)
                    {
                        if (index < 8255)
                        {
                            if (index < 8252)
                            {
                                if (index < 8251)
                                    return 1;
                                else
                                    return 2;
                            }
                            else
                            {
                                if (index < 8254)
                                    return 1;
                                else
                                    return 2;
                            }
                        }
                        else
                        {
                            if (index < 8365)
                            {
                                if (index < 8364)
                                    return 1;
                                else
                                    return 2;
                            }
                            else
                            {
                                if (index < 8451)
                                    return 1;
                                else
                                    return 2;
                            }
                        }
                    }
                    else
                    {
                        if (index < 8458)
                        {
                            if (index < 8454)
                            {
                                if (index < 8453)
                                    return 1;
                                else
                                    return 2;
                            }
                            else
                            {
                                if (index < 8457)
                                    return 1;
                                else
                                    return 2;
                            }
                        }
                        else
                        {
                            if (index < 8471)
                            {
                                if (index < 8470)
                                    return 1;
                                else
                                    return 2;
                            }
                            else
                            {
                                return 1;
                            }
                        }
                    }
                }
                else
                {
                    if (index < 8596)
                    {
                        if (index < 8560)
                        {
                            if (index < 8544)
                            {
                                if (index < 8482)
                                    return 2;
                                else
                                    return 1;
                            }
                            else
                            {
                                if (index < 8556)
                                    return 2;
                                else
                                    return 1;
                            }
                        }
                        else
                        {
                            if (index < 8592)
                            {
                                if (index < 8570)
                                    return 2;
                                else
                                    return 1;
                            }
                            else
                            {
                                return 2;
                            }
                        }
                    }
                    else
                    {
                        if (index < 8713)
                        {
                            if (index < 8602)
                            {
                                if (index < 8598)
                                    return 1;
                                else
                                    return 2;
                            }
                            else
                            {
                                if (index < 8712)
                                    return 1;
                                else
                                    return 2;
                            }
                        }
                        else
                        {
                            if (index < 8720)
                            {
                                if (index < 8719)
                                    return 1;
                                else
                                    return 2;
                            }
                            else
                            {
                                return 1;
                            }
                        }
                    }
                }
            }
            else
            {
                if (index < 8743)
                {
                    if (index < 8731)
                    {
                        if (index < 8728)
                        {
                            if (index < 8725)
                            {
                                if (index < 8722)
                                    return 2;
                                else
                                    return 1;
                            }
                            else
                            {
                                if (index < 8726)
                                    return 2;
                                else
                                    return 1;
                            }
                        }
                        else
                        {
                            if (index < 8730)
                            {
                                if (index < 8729)
                                    return 2;
                                else
                                    return 1;
                            }
                            else
                            {
                                return 2;
                            }
                        }
                    }
                    else
                    {
                        if (index < 8740)
                        {
                            if (index < 8737)
                            {
                                if (index < 8733)
                                    return 1;
                                else
                                    return 2;
                            }
                            else
                            {
                                if (index < 8739)
                                    return 1;
                                else
                                    return 2;
                            }
                        }
                        else
                        {
                            if (index < 8742)
                            {
                                if (index < 8741)
                                    return 1;
                                else
                                    return 2;
                            }
                            else
                            {
                                return 1;
                            }
                        }
                    }
                }
                else
                {
                    if (index < 8766)
                    {
                        if (index < 8756)
                        {
                            if (index < 8750)
                            {
                                if (index < 8748)
                                    return 2;
                                else
                                    return 1;
                            }
                            else
                            {
                                if (index < 8751)
                                    return 2;
                                else
                                    return 1;
                            }
                        }
                        else
                        {
                            if (index < 8764)
                            {
                                if (index < 8760)
                                    return 2;
                                else
                                    return 1;
                            }
                            else
                            {
                                return 2;
                            }
                        }
                    }
                    else
                    {
                        if (index < 8781)
                        {
                            if (index < 8777)
                            {
                                if (index < 8776)
                                    return 1;
                                else
                                    return 2;
                            }
                            else
                            {
                                if (index < 8780)
                                    return 1;
                                else
                                    return 2;
                            }
                        }
                        else
                        {
                            if (index < 8787)
                            {
                                if (index < 8786)
                                    return 1;
                                else
                                    return 2;
                            }
                            else
                            {
                                return 1;
                            }
                        }
                    }
                }
            }
        }
    }
    else
    {
        if (index < 12549)
        {
            if (index < 9676)
            {
                if (index < 8979)
                {
                    if (index < 8857)
                    {
                        if (index < 8814)
                        {
                            if (index < 8804)
                            {
                                if (index < 8802)
                                    return 2;
                                else
                                    return 1;
                            }
                            else
                            {
                                if (index < 8808)
                                    return 2;
                                else
                                    return 1;
                            }
                        }
                        else
                        {
                            if (index < 8853)
                            {
                                if (index < 8816)
                                    return 2;
                                else
                                    return 1;
                            }
                            else
                            {
                                if (index < 8854)
                                    return 2;
                                else
                                    return 1;
                            }
                        }
                    }
                    else
                    {
                        if (index < 8895)
                        {
                            if (index < 8869)
                            {
                                if (index < 8858)
                                    return 2;
                                else
                                    return 1;
                            }
                            else
                            {
                                if (index < 8870)
                                    return 2;
                                else
                                    return 1;
                            }
                        }
                        else
                        {
                            if (index < 8978)
                            {
                                if (index < 8896)
                                    return 2;
                                else
                                    return 1;
                            }
                            else
                            {
                                return 2;
                            }
                        }
                    }
                }
                else
                {
                    if (index < 9650)
                    {
                        if (index < 9372)
                        {
                            if (index < 9322)
                            {
                                if (index < 9312)
                                    return 1;
                                else
                                    return 2;
                            }
                            else
                            {
                                if (index < 9332)
                                    return 1;
                                else
                                    return 2;
                            }
                        }
                        else
                        {
                            if (index < 9634)
                            {
                                if (index < 9632)
                                    return 1;
                                else
                                    return 2;
                            }
                            else
                            {
                                return 1;
                            }
                        }
                    }
                    else
                    {
                        if (index < 9670)
                        {
                            if (index < 9660)
                            {
                                if (index < 9652)
                                    return 2;
                                else
                                    return 1;
                            }
                            else
                            {
                                if (index < 9662)
                                    return 2;
                                else
                                    return 1;
                            }
                        }
                        else
                        {
                            if (index < 9675)
                            {
                                if (index < 9672)
                                    return 2;
                                else
                                    return 1;
                            }
                            else
                            {
                                return 2;
                            }
                        }
                    }
                }
            }
            else
            {
                if (index < 12293)
                {
                    if (index < 9738)
                    {
                        if (index < 9702)
                        {
                            if (index < 9680)
                            {
                                if (index < 9678)
                                    return 1;
                                else
                                    return 2;
                            }
                            else
                            {
                                if (index < 9698)
                                    return 1;
                                else
                                    return 2;
                            }
                        }
                        else
                        {
                            if (index < 9735)
                            {
                                if (index < 9733)
                                    return 1;
                                else
                                    return 2;
                            }
                            else
                            {
                                if (index < 9737)
                                    return 1;
                                else
                                    return 2;
                            }
                        }
                    }
                    else
                    {
                        if (index < 9795)
                        {
                            if (index < 9793)
                            {
                                if (index < 9792)
                                    return 1;
                                else
                                    return 2;
                            }
                            else
                            {
                                if (index < 9794)
                                    return 1;
                                else
                                    return 2;
                            }
                        }
                        else
                        {
                            if (index < 12292)
                            {
                                if (index < 12288)
                                    return 1;
                                else
                                    return 2;
                            }
                            else
                            {
                                return 1;
                            }
                        }
                    }
                }
                else
                {
                    if (index < 12436)
                    {
                        if (index < 12321)
                        {
                            if (index < 12317)
                            {
                                if (index < 12312)
                                    return 2;
                                else
                                    return 1;
                            }
                            else
                            {
                                if (index < 12319)
                                    return 2;
                                else
                                    return 1;
                            }
                        }
                        else
                        {
                            if (index < 12353)
                            {
                                if (index < 12330)
                                    return 2;
                                else
                                    return 1;
                            }
                            else
                            {
                                return 2;
                            }
                        }
                    }
                    else
                    {
                        if (index < 12535)
                        {
                            if (index < 12447)
                            {
                                if (index < 12443)
                                    return 1;
                                else
                                    return 2;
                            }
                            else
                            {
                                if (index < 12449)
                                    return 1;
                                else
                                    return 2;
                            }
                        }
                        else
                        {
                            if (index < 12543)
                            {
                                if (index < 12540)
                                    return 1;
                                else
                                    return 2;
                            }
                            else
                            {
                                return 1;
                            }
                        }
                    }
                }
            }
        }
        else
        {
            if (index < 55297)
            {
                if (index < 13215)
                {
                    if (index < 12959)
                    {
                        if (index < 12832)
                        {
                            if (index < 12690)
                            {
                                if (index < 12586)
                                    return 2;
                                else
                                    return 1;
                            }
                            else
                            {
                                if (index < 12704)
                                    return 2;
                                else
                                    return 1;
                            }
                        }
                        else
                        {
                            if (index < 12928)
                            {
                                if (index < 12868)
                                    return 2;
                                else
                                    return 1;
                            }
                            else
                            {
                                if (index < 12958)
                                    return 2;
                                else
                                    return 1;
                            }
                        }
                    }
                    else
                    {
                        if (index < 13198)
                        {
                            if (index < 12969)
                            {
                                if (index < 12964)
                                    return 2;
                                else
                                    return 1;
                            }
                            else
                            {
                                if (index < 12977)
                                    return 2;
                                else
                                    return 1;
                            }
                        }
                        else
                        {
                            if (index < 13212)
                            {
                                if (index < 13200)
                                    return 2;
                                else
                                    return 1;
                            }
                            else
                            {
                                return 2;
                            }
                        }
                    }
                }
                else
                {
                    if (index < 13265)
                    {
                        if (index < 13253)
                        {
                            if (index < 13218)
                            {
                                if (index < 13217)
                                    return 1;
                                else
                                    return 2;
                            }
                            else
                            {
                                if (index < 13252)
                                    return 1;
                                else
                                    return 2;
                            }
                        }
                        else
                        {
                            if (index < 13263)
                            {
                                if (index < 13262)
                                    return 1;
                                else
                                    return 2;
                            }
                            else
                            {
                                return 1;
                            }
                        }
                    }
                    else
                    {
                        if (index < 19968)
                        {
                            if (index < 13269)
                            {
                                if (index < 13267)
                                    return 2;
                                else
                                    return 1;
                            }
                            else
                            {
                                if (index < 13270)
                                    return 2;
                                else
                                    return 1;
                            }
                        }
                        else
                        {
                            if (index < 55296)
                            {
                                if (index < 40870)
                                    return 2;
                                else
                                    return 1;
                            }
                            else
                            {
                                return 0;
                            }
                        }
                    }
                }
            }
            else
            {
                if (index < 65093)
                {
                    if (index < 63733)
                    {
                        if (index < 59335)
                        {
                            if (index < 56321)
                            {
                                if (index < 56320)
                                    return 1;
                                else
                                    return 2;
                            }
                            else
                            {
                                if (index < 57344)
                                    return 1;
                                else
                                    return 2;
                            }
                        }
                        else
                        {
                            if (index < 59493)
                            {
                                if (index < 59337)
                                    return 1;
                                else
                                    return 2;
                            }
                            else
                            {
                                return 1;
                            }
                        }
                    }
                    else
                    {
                        if (index < 65072)
                        {
                            if (index < 63744)
                            {
                                if (index < 63734)
                                    return 2;
                                else
                                    return 1;
                            }
                            else
                            {
                                if (index < 64046)
                                    return 2;
                                else
                                    return 1;
                            }
                        }
                        else
                        {
                            if (index < 65075)
                            {
                                if (index < 65074)
                                    return 2;
                                else
                                    return 1;
                            }
                            else
                            {
                                return 2;
                            }
                        }
                    }
                }
                else
                {
                    if (index < 65128)
                    {
                        if (index < 65112)
                        {
                            if (index < 65107)
                            {
                                if (index < 65097)
                                    return 1;
                                else
                                    return 2;
                            }
                            else
                            {
                                if (index < 65108)
                                    return 1;
                                else
                                    return 2;
                            }
                        }
                        else
                        {
                            if (index < 65127)
                            {
                                if (index < 65113)
                                    return 1;
                                else
                                    return 2;
                            }
                            else
                            {
                                return 1;
                            }
                        }
                    }
                    else
                    {
                        if (index < 65504)
                        {
                            if (index < 65281)
                            {
                                if (index < 65132)
                                    return 2;
                                else
                                    return 1;
                            }
                            else
                            {
                                if (index < 65375)
                                    return 2;
                                else
                                    return 1;
                            }
                        }
                        else
                        {
                            if (index < 65536)
                            {
                                if (index < 65510)
                                    return 2;
                                else
                                    return 1;
                            }
                            else
                            {
                                return 0;
                            }
                        }
                    }
                }
            }
        }
    }
}
```

或者, 我们将这个列表, 写入到byte[], 每一个字符占1bit, 如果对应bit是1, 则表示字符宽度为2, 如果对应位是0, 则表示字符宽度为1. 这样, **速度是更快的**, 只不过要**牺牲8kb的空间**了.


因为控制台中, 字符宽度只有4中可能, 0, 1, 2, 8, 其中0是\r\n\0这类无宽度的, 显然它们也无法参与字符串宽度判断, 1就是普通半角字符, 2就是中文以及全角字符, 8显然是\t, 故, 我们只需要能够表示1和2即可, 也就是1bit的空间.


```csharp
int[] lens = new int[char.MaxValue + 1]
void WriteData()    // 将已经生成好的宽度数据写入到文件
{
    FileStream fs = File.OpenWrite(Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.Desktop), "Lengths.bin"));
    byte[] write = new byte[1];
    for (int i = 0; i < lens.Length;)
    {
        for (int j = 0; j < 8 && i < lens.Length; i++, j++)
        {
            if (lens[i] == 2)
                write[0] |= (byte)(1 << j);
        }
        fs.Write(write, 0, 1);
    }
    fs.Flush();
    fs.Close();
}
```


char.MaxValue / 8 = 8k, 8kb即可装下所有字符的宽度信息.


然后结合位运算, 就能得出一个简单的函数. 例如 Lengths 是我们的byte数组.

```csharp
static byte[] Lengths = Resources.Lengths;
public static bool IsWideDisplayCharEx(char c)
{
    int index = c;
    return (Lengths[index / 8] & (1 << (index % 8))) != 0;
}
public static int GetCharDisplayLengthEx(char c)
{
    return IsWideDisplayCharEx(c) ? 2 : 1;
}
public static int GetStringDisplayLengthEx(string str)
{
    int total = 0;
    foreach (char c in str)
        total += GetCharDisplayLengthEx(c);
    return total;
}
```

### 关于使用:

直接去clone开头所写明的代码仓库即可, 会不定时更新的.或者, 直接复制上面的代码(最长的那一段).


上面的代码大部分是自动生成的哦~![偷懒](images/20210204201122821.png)
关于图中使用到的Null.TextEditor, 在这里:

1. CSDN 站内链接: [使用IronPython来创建支持脚本编辑文本内容的文本编辑器](https://blog.csdn.net/m0_46555380/article/details/113578320)
2. Github 仓库地址: [SlimeNull/Null.TextEditor](https://github.com/SlimeNull/Null.TextEditor)


<br/><br/><br/><br/><br/><br/><br/><br/><br/>


点个赞, 留个评论再走吧QAQ
