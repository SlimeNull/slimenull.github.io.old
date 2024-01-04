---
title: 'Kali Linux 记录 - 死锁，依赖 ‘Unable to install new version of ...libpng12.so.0 no such file or directory‘'
slug: '20201208195444'
date: 2020-12-08T19:54:44+08:00
tags:
  - linux
  - bug
  - apt
  - dpkg
categories:
  - Linux
  - 成长记录
  - Debian
description: '概述:在使用apt或dpkg安装deb包时， 出现了no such file or directory的错误要点:在dpkg时是否指定了错误的路径包对于系统来说是否过旧我的解决过程:我遇到的问题属于要点中的后者， libpng12对于我的系统来说太旧了。具体情况是这样的， 我在使用apt安装一些软件时， 发现， 一直报依赖问题， 让我执行‘apt --fix-broken install’(即‘apt -f install’)， 但当我执行时， 它尝试安装的libpng12-0始'
---

## 概述:

0. 在使用apt或dpkg安装deb包时， 出现了no such file or directory的错误

## 要点:

0. 在dpkg时是否指定了错误的路径
1. 包对于系统来说是否过旧

## 我的解决过程:

- 我遇到的问题属于要点中的后者， libpng12对于我的系统来说太旧了。

> 具体情况是这样的， 我在使用apt安装一些软件时， 发现， 一直报依赖问题， 让我执行‘apt --fix-broken install’(即‘apt -f install’)， 但当我执行时， 它尝试安装的libpng12-0始终装不上(都快把孩子急哭了qaq)！ 从报错信息上看， libpng12-0似乎是libpng12.so.0的新版本， 在安装时总是提示no such file or directory。

0. 首先， 我在网上搜索了一波， 但是依照国内IT圈子转载满天飞的尿性， 我还是以失败告终 —— 找了几十篇文章也没看到有用的
1. 后来我发现， 如果我不处理这个错误， 我不能进行任何apt install， upgrade， remove操作！ 我慌了， 于是我尝试执行apt remove libpng12.so.0， 但是提示它被libwebkitgtk什么的一个我不认识的东西包所依赖， 然后， 不能移除。。。
2. 然后我又尝试移除这个依赖于libpng的libwebkitgtk的什么包， 然后我发现它又被sunloginclient(应该是向日葵远程控制软件客户端)所依赖，，， 移除失败，，，
3. 于是我尝试移除sunloginclient这个包（反正我目前不大用得到向日葵）， 但是，，，正如我前面所说的， 虽然sunloginclient不被什么包所依赖， 但是因为我们libpng12-0的依赖问题没解决， 所以，，， 移除失败。。。
4. 于是， 我似乎陷入了一个逻辑问题， 想要处理这个依赖问题， 我就得移除掉旧的libpng12.so.0， 想要移除它我就得移除sunloginclient然后最后移除它， 而只有我处理完依赖问题后， 才能够使用apt来进行安装与移除操作！

<table align="center"><tr><td>绝了</td></tr><tr><td><img align="center" width="100" height="100" src="https://i0.hdslb.com/bfs/emote/35d62c496d1e4ea9e091243fa812866f5fecc101.png@112w_112h.png"/></td></tr></table>


5. 查资料， 查资料， 查资料， 经过一段时间的周旋， 我顿悟了， 很快啊， 我打开终端， 用dpkg把sunloginclient给移除了， 然后顺理成章的把libwebkitgtk， libpng12.so.0给移除了

> 完美解决
