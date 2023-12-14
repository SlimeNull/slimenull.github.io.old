---
title: 'Ubuntu 新手引导 - 中文输入法配置'
date: 2020-12-17 23:39:37
tags:
  - ubuntu
  - linux
categories:
  - Debian
  - 成长记录
  - 笔记
description: '选择输入法框架：Linux中，有多个输入法框架可供使用，在 Ubuntu 中，默认预装了 ibus 这款输入法引擎，常用的中文输入法引擎有两种，ibus 与 fcitx。两者都还彳亍，懒得下载的话，就直接用ibus吧。配置使用的输入法框架：打开终端，执行 ‘im-config’，在弹出的窗口中，选择ok，yes，然后选择 ibus，于是，你就成功使用了ibus框架。同理，选择 fcitx 则是使用 fcitx 框架，至于安装 fcitx，见文章末。选择输入法引擎：关键的时刻到咯，那么对于ibu'
---

## 选择输入法框架：

1. Linux中，有多个输入法框架可供使用，在 Ubuntu 中，默认预装了 ibus 这款输入法引擎，常用的中文输入法引擎有两种，ibus 与 fcitx。两者都还彳亍，懒得下载的话，就直接用ibus吧。
2. 配置使用的输入法框架：打开终端，执行 'im-config'，在弹出的窗口中，选择ok，yes，然后选择 ibus，于是，你就成功使用了ibus框架。同理，选择 fcitx 则是使用 fcitx 框架，至于安装 fcitx，见文章末。

## 选择输入法引擎：

1. 关键的时刻到咯，那么对于ibus，常用的输入法有：rime，sunpinyin，googlepinyin(谷歌拼音)，我的话，推荐rime和sunpinyin。
2. 对于fcitx嘛，常用的有sogoupinyin(搜狗拼音)，rime，sunpinyin，googlepinyin，搜狗的话，其实还是很棒的(没有广告，毕竟谁会往Linux系统软件中投放广告呢,,,)，然后rime和sunpinyin，也都蛮棒咯。
3. 至于安装，见文章末。

## 添加输入法引擎：

1. ibus的话，打开终端，执行 'ibus-setup'，然后在 'Input method' 选项卡中就可以添加了。（什么？不会操作？建议重修英文）
2. fcitx的话，更简单了，打开 Ubuntu 应用列表(我指屏幕左下角的按钮)，搜索fcitx，打开fcitx configuration，然后，你懂得，添加输入法就好。实在不会，参考这篇文章吧 [在Kali中配置中文输入法](https://editor.csdn.net/md/?articleId=110823818)
3. 最后一点，如果你在添加输入法引擎的时候，找不到自己安装的输入法，先确认自己的输入法框架是否选择正确了，也就是在 'im-config' 中查看，二就是尝试重启。

## 添加输入法源：

1. 重启计算机以保证上面的更改生效，否则无法添加输入法源。
2. 打开Settings（设置）
3. 找到 Region & Language (地区和语言)。
4. 在 Input Source (输入源) 中点击 '+' 添加输入源，选择自己安装的输入法。它一般位于 Chinese (汉语) 组中，例如 'Chinese (SunPinyin)'

> 再次强调，如果你找不到，就尝试重启
 

![在这里插入图片描述](images/20201217231817146.png)

## 享受打字：

- 如果你走的是 ibus 线，那么现在，你现在可以通过 Super + Space(空格) 来切换输入方式了。
- 如果你走的是 fcitx 线，则是通过 Ctrl + Space(空格) 来切换输入法，并且你可以在 fcitx configuration 中设置甚至添加更换输入法的快捷键。

> 在 Ubuntu 中， Super 键对应着 Windows 徽标键





<br/><br/>

##### 安装输入法框架：

- 哦？不会安装了吗？让我来帮助你吧。
- 安装 ibus 使用 'sudo apt install ibus' 指令即可。
- 安装 fcitx 使用 'sudo apt install fcitx' 即可。

> 下载速度慢？ 如果不是网络问题的话，就尝试更换软件安装源吧。这个在网络上可以很容易的搜索到。个人建议 阿里云镜像 以及 华为云镜像。

##### 安装输入法引擎：

- 其实这件事情也蛮简单的，因为 apt 可以搜索软件，通过 'apt search ibus' 就可以搜索到关于 ibus 的软件，其中不乏有许多 ibus 的输入法引擎
- 输入法引擎的命名方式很简单，就是 框架名-引擎名，例如 ibus-rime，直接 'sudo apt install ibus-rime' 即可安装适用于 ibus 的 rime 输入法。本文章提到的其他输入法引擎也是如此(只有sogoupinyin需要在[SogouPinyin官网](https://pinyin.sogou.com/linux/)下载)


##### 其他小问题：

- 不知为何，Ubuntu中 ibus 有点问题，它无法设置为候选词水平显示，网络上也找不到解决方式，如果你介意这一点，那么就使用fcitx框架吧。


##### *作者说:*


- 如果可以的话，为此文章点赞，并且加入到写文章的行列，国内IT圈环境需要我们做出贡献！
