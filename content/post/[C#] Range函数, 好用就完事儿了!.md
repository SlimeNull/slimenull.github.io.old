---
title: '[C#] Range函数, 好用就完事儿了!'
slug: '20210219070741'
date: 2021-02-19T07:07:41+08:00
tags:
  - csharp
  - dotnet
  - csharp
categories:
  - 类库
  - dotnet
  - note
description: '这么多重载, 完全够用了~, 返回值是 IEnumerable<T>using System;using System.Collections;using System.Collections.Generic;namespace Null.Library{    public static partial class Lib    {        public static IEnumerable<int> Range(int stop)        {  '
---

这么多重载, 完全够用了~, 返回值是 IEnumerable&lt;T&gt;
其中, Range函数是简单的循环, RangeEx加入了检测, 不会造成死循环, 且精度非常准确

```csharp
using System;
using System.Collections.Generic;

namespace NullLib.Range
{
    public class NRange
    {
        public static IEnumerable<int> Range(int stop)
        {
            for (int i = 0; i < stop; i++)
                yield return i;
        }
        public static IEnumerable<int> Range(int start, int stop)
        {
            for (int i = start; i < stop; i++)
                yield return i;
        }
        public static IEnumerable<int> Range(int start, int stop, int step)
        {
            for (int i = start; i < stop; i += step)
                yield return i;
        }
        public static IEnumerable<float> Range(float stop)
        {
            for (int i = 0; i < stop; i++)
                yield return i;
        }
        public static IEnumerable<float> Range(float start, float stop)
        {
            for (float i = start; i < stop; i++)
                yield return i;
        }
        public static IEnumerable<float> Range(float start, float stop, float step)
        {
            for (float i = start; i < stop; i += step)
                yield return i;
        }
        public static IEnumerable<double> Range(double stop)
        {
            for (int i = 0; i < stop; i++)
                yield return i;
        }
        public static IEnumerable<double> Range(double start, double stop)
        {
            for (double i = start; i < stop; i++)
                yield return i;
        }
        public static IEnumerable<double> Range(double start, double stop, double step)
        {
            for (double i = start; i < stop; i += step)
                yield return i;
        }
        public static IEnumerable<int> RangeEx(int stop)
        {
            int start = 0, step = 1, current = start;
            step = step < 0 ? -step : step;
            if (start < stop)
                for (int i = 0; current < stop; i++, current = step * i + start)
                    yield return current;
            else
                for (int i = 0; current > stop; i--, current = step * i + start)
                    yield return current;
        }
        public static IEnumerable<int> RangeEx(int start, int stop)
        {
            int step = 1, current = start;
            step = step < 0 ? -step : step;
            if (start < stop)
                for (int i = 0; current < stop; i++, current = step * i + start)
                    yield return current;
            else
                for (int i = 0; current > stop; i--, current = step * i + start)
                    yield return current;
        }
        public static IEnumerable<int> RangeEx(int start, int stop, int step)
        {
            int current = start;
            step = step < 0 ? -step : step;
            if (start < stop)
                for (int i = 0; current < stop; i++, current = step * i + start)
                    yield return current;
            else
                for (int i = 0; current > stop; i--, current = step * i + start)
                    yield return current;
        }
        public static IEnumerable<float> RangeEx(float stop)
        {
            float start = 0, step = 1, current = start;
            step = step < 0 ? -step : step;
            if (start < stop)
                for (int i = 0; current < stop; i++, current = step * i + start)
                    yield return current;
            else
                for (int i = 0; current > stop; i--, current = step * i + start)
                    yield return current;
        }
        public static IEnumerable<float> RangeEx(float start, float stop)
        {
            float step = 1, current = start;
            step = step < 0 ? -step : step;
            if (start < stop)
                for (int i = 0; current < stop; i++, current = step * i + start)
                    yield return current;
            else
                for (int i = 0; current > stop; i--, current = step * i + start)
                    yield return current;
        }
        public static IEnumerable<float> RangeEx(float start, float stop, float step)
        {
            float current = start;
            step = step < 0 ? -step : step;
            if (start < stop)
                for (int i = 0; current < stop; i++, current = step * i + start)
                    yield return current;
            else
                for (int i = 0; current > stop; i--, current = step * i + start)
                    yield return current;
        }
        public static IEnumerable<double> RangeEx(double stop)
        {
            double start = 0, step = 1, current = start;
            step = step < 0 ? -step : step;
            if (start < stop)
                for (int i = 0; current < stop; i++, current = step * i + start)
                    yield return current;
            else
                for (int i = 0; current > stop; i--, current = step * i + start)
                    yield return current;
        }
        public static IEnumerable<double> RangeEx(double start, double stop)
        {
            double step = 1, current = start;
            step = step < 0 ? -step : step;
            if (start < stop)
                for (int i = 0; current < stop; i++, current = step * i + start)
                    yield return current;
            else
                for (int i = 0; current > stop; i--, current = step * i + start)
                    yield return current;
        }
        public static IEnumerable<double> RangeEx(double start, double stop, double step)
        {
            double current = start;
            step = step < 0 ? -step : step;
            if (start < stop)
                for (int i = 0; current < stop; i++, current = step * i + start)
                    yield return current;
            else
                for (int i = 0; current > stop; i--, current = step * i + start)
                    yield return current;
        }
    }
}
```
