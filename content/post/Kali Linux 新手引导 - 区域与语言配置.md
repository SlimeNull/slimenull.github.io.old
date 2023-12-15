---
title: 'Kali Linux 新手引导 - 区域与语言配置'
slug: 'KaliLinux新手引导-区域与语言配置'
date: 2020-12-07 16:50:05
tags:
  - linux
  - debian
  - kali linux
  - xfce
categories:
  - note
  - 成长记录
description: '关于我的Kali系统：操作系统：Linux NullKali 5.9.0-kali2-amd64 #1 SMP Debian 5.9.6-1kali1 (2020-11-11) x86_64 GNU/Linux桌面环境：Xfce Desktop Environment Version 4.14, destributed by Debian配置区域tzselect# 在Shell中执行即可， 内容来自网络配置语言sudo dpkg-reconfigure locales# 配置完后记得重'
---

#### 关于我的Kali系统：

> 操作系统：Linux NullKali 5.9.0-kali2-amd64 #1 SMP Debian 5.9.6-1kali1 (2020-11-11) x86_64 GNU/Linux
> 桌面环境：Xfce Desktop Environment Version 4.14, destributed by Debian

## 配置区域

```shell
tzselect
# 在Shell中执行即可， 内容来自网络
```

## 配置语言

```shell
sudo dpkg-reconfigure locales
# 配置完后记得重启(reboot)
# 这条指令的意思应该是调整语言并安装相关包
# 配置完语言后，在登录页面的右上角可以快捷切换语言
```
