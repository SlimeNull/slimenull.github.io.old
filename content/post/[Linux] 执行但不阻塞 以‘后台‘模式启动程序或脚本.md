---
title: '[Linux] 执行但不阻塞 以‘后台‘模式启动程序或脚本'
slug: '[Linux]执行但不阻塞以‘后台‘模式启动程序或脚本'
date: 2020-12-18 01:16:10
tags:
  - linux
  - shell
  - ubuntu
categories:
  - Linux
  - 成长记录
description: '1. 使用 ‘&’ 符号：例如启动一个脚本，执行 ‘./idea.sh’ 以启动idea，但终端会被阻塞，若终端被关闭，idea也就被关闭。此时，执行 ‘./idea.sh &’ 可以使 idea 脱离当前终端运行，即便当前终端被关闭，idea也不会受影响。./idea.sh     # 会阻塞当前终端进程./idea.sh &   # 不会阻塞2. 使用 ‘nohup’ 指令：参考 菜鸟教程 - nohup指令参考文章：Linux后台执行命令(非阻塞式)'
---

## 1. 使用 '&' 符号：

- 例如启动一个脚本，执行 './idea.sh' 以启动idea，但终端会被阻塞，若终端被关闭，idea也就被关闭。
- 此时，执行 './idea.sh &' 可以使 idea 脱离当前终端运行，即便当前终端被关闭，idea也不会受影响。

```shell
./idea.sh     # 会阻塞当前终端进程
./idea.sh &   # 不会阻塞
```

## 2. 使用 'nohup' 指令：

- 参考 [菜鸟教程 - nohup指令](https://www.runoob.com/linux/linux-comm-nohup.html)


参考文章：[Linux后台执行命令(非阻塞式)](https://blog.p2hp.com/archives/5528)
