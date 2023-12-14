---
title: '[C#,笔记] NAudio 傅里叶变换的 forward 参数与 m 参数'
slug: '[CSharp,笔记]NAudio傅里叶变换的forward参数与m参数'
date: 2022-10-25 07:48:33
tags:
  - 开发语言
  - 音频
categories:
  - .NET
  - 笔记
description: 'NAudio FourierTransform.FFT 的参数值'
---

forward 是是否对结果取平均值 (除以样本数), 这样样本数量就不会影响结果值的大小.
m 是要进行变换的数量, 但是是 2 的指数, 也就是说 2^m 必须小于等于样本数
因为傅里叶变换只能进行 2^m 个数据的傅里叶变换
