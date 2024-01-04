---
title: 'Kali Linux 新手引导 - 配置中文输入法'
slug: 'KaliLinux新手引导-配置中文输入法'
date: 2020-12-07T20:35:24+08:00
tags:
  - kali linux
  - debian
  - linux
categories:
  - 成长记录
  - Linux
  - Kali
description: '关于我的Kali系统：操作系统：Linux NullKali 5.9.0-kali2-amd64 #1 SMP Debian 5.9.6-1kali1 (2020-11-11) x86_64 GNU/Linux桌面环境：Xfce Desktop Environment Version 4.14, destributed by Debian安装输入法框架Fcitxsudo apt install fcitx# kali默认是装了fcitx的， 如果没有安装， 就执行上边的指令安装吧安装好用的'
---

#### 关于我的Kali系统：

> 操作系统：Linux NullKali 5.9.0-kali2-amd64 #1 SMP Debian 5.9.6-1kali1 (2020-11-11) x86_64 GNU/Linux
> 桌面环境：Xfce Desktop Environment Version 4.14, destributed by Debian

## 安装输入法框架Fcitx

```shell
sudo apt install fcitx
# 这里推荐的是Fcitx， kali默认是装了fcitx的， 如果没有安装， 就执行上边的指令安装吧
```

## 安装好用的输入法

访问 ‘搜狗输入法 for Linux’ 官网并下载deb包。[点此处跳转](https://pinyin.sogou.com/linux/)

```shell
sudo dpkg -i sogoupinyin_版本号_amd64.deb
# 若出现依赖问题， 则执行指令以尝试修复: sudo apt -f install
```

## 配置输入法



1. 启动 Fcitx Configuration ， 点击左下角加号以添加输入法， 取消勾选‘Only Show Current Language’， 找到sogoupinyiin （没错， 不是sougou而是sogou）
   <img src="https://img-blog.csdnimg.cn/20201207180141330.png"/>
3. 现在， 一切就绪， 你可以使用输入法了。 默认的输入法切换快捷键是 ‘Ctrl + Space’ ， 可在Fcitx Configuration的Global Config中调整。切换至‘搜狗拼音’时， 在屏幕上会出现在搜狗拼音的语言栏， 如下：
   <img src="https://img-blog.csdnimg.cn/20201207180555556.png"/>
4. 此时， 你可以正常使用输入法。若有其他问题， 可继续阅读下面的内容。

<table>
<tr>
<td>
<span><font color="#888">下图即配置好的Fcitx</font></span>
<img src="https://img-blog.csdnimg.cn/20201207180031275.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzQ2NTU1Mzgw,size_16,color_FFFFFF,t_70"/>
</td>
</tr>
</table>


## 玄学问题：

1. ###### Fcitx Configuration 启动后， 窗口中没有任何内容（正常情况下应该有一个Keyboard - 语言）
    > 尝试重启， 重新安装Fcitx， 更换区域与语言（参阅：[Kali Linux 新手引导 - 区域与语言配置](https://blog.csdn.net/m0_46555380/article/details/110821855)）
2. ###### 在Fcitx Configuration 中添加输入法时， 无法找到sogoupinyin
    > 检查输入法是否正确安装了， 尝试执行apt update， apt upgrade， apt -f install
