---
title: '[全解] 刷机, BL 锁, Bootloader, Recovery, Magisk, Root, ADB, 线刷, 卡刷, 9008, 绕过 FRP'
date: 2022-10-22 10:43:02
tags:
  - adb
  - android
categories:
  - 笔记
description: '刷机, BL 锁, Bootloader, Recovery, Magisk, Root, ADB, 线刷, 卡刷, 9008, 绕过 FRP 全解'
---

这篇文章讲解手机刷机, Root 的教程, 以及过程中可能遇到的大多数问题.


> 你可能需要对电脑重装系统有些了解, 知道分区, 设备, 驱动是什么东西, 并且能够熟悉使用电脑, 以及在命令窗口中执行命令


## 概述

手机刷机, 当然也可以说成重装系统. 一般我们使用电脑重装系统的时候, 都是通过 PE 或者写入了安装镜像的存储介质, 引导, 然后安装, 非常简单. 但是手机刷机的过程与电脑不一样.


电脑之所以能够随便重装系统, 那是因为电脑中的设备, 都有着相同的通讯协议, 以及电脑有着完善的驱动, 可以随便使用. 例如我插入了一个电脑无法识别的设备, 那么只需要从官网下载其对应的驱动, 那么电脑就可以使用这个设备了.


而且电脑中很多设备, 都是通用的协议, 例如存储设备, 摄像机, 音频设备之类的, 这些设备很多都不需要为其安装专用驱动即可直接使用.


### 1. 分区

对于电脑来讲, 用户可以随意分区, 但是手机的分区都是预先分好的, 使用者在使用的过程中, 一般是不会对其进行重新分区, 而且手机中的分区也都指定好了作用.


> 手机分区包含: modem, bootloader, boot, recovery, system, data, cache


**bootloader:**
其中, bootloader 分区, 如其名, 用来引导启动, 可以理解为类似于电脑中 MBR 或者 GRUB 的存在, 其中存放着用来引导启动的程序.


**recovery:**
recovery 分区则用来进行系统的一些高级的维护, 例如恢复出厂设置什么的. 一般的, 手机默认携带的 recovery 分区内的程序, 不包含高级功能, 但是我们可以通过刷入第三方的 recovery, 实现更多功能, 这也是刷机的主要步骤.


**boot:**
boot 分区包含系统的内核 (Linux)


**system:**
系统分区, 包含了系统本体, 除了内核以外的所有部分


### 2. 高权限

对于电脑来讲, 通过外部设备引导进入 PE, 于是就能够随心所欲的对物理机中的其他设备进行任何操作, 例如重新分区, 安装系统什么的, 但是安卓不能这样.


对于安卓来讲, 首先我们要进入 bootloader, 在开机时长按 `音量-` 和 `电源` 键, 即可进入 bootloader, 此时通过数据线将电脑与手机链接, 然后电脑就可以通过 "fastboot" 工具对手机进行操作


但是手机一般都会对 bootloader 进行限制, 使你无法进行危险操作, 例如刷入第三方 recovery 之类的, 所以在使用 fastboot 之前, 你还需要将 BL 锁给解除.


> 目前一加, 小米这两个厂商开放性比较强, 官方提供了解除 BL 锁的方案, OPPO 和 VIVO 则封闭, 基本上没办法刷机与 ROOT (除了旧手机)


## 解除 BL 锁

对于开放性最强的一加, 解除 BL 锁没有任何难度, 但是不同型号有不同的解除方式, 有的只需要执行指令, 有的则需要进入开发者选项打开 OEM 解锁.


对于 小米/红米 手机, 则需要使用官方的解锁工具进行解锁, 并且还需要将手机与小米账户绑定, 然后等待一个星期什么的, 总之挺麻烦的.


OPPO / vivio 的话, 就完全不用想刷机了,,, 很难的啦, 哦对了, 华为也一样


> 在使用官方提供的解 BL 锁工具时, 设备可能识别不到, 你需要先安装设备对应的驱动才可以, 而对应工具中一般都提供了这个功能.


> 网上也有很多强制解 BL 锁的奇妙方法, 甚至连 OPPO 强解 BL 锁的都有, 但是不适用于所有设备, 你也可以尝试, 但是解了 BL 锁, 最多弄个 Root 权限, 刷第三方系统就算了, 因为他们不适配 OPPO / vivo 的设备


## 准备工作

**下载安卓平台工具 (ADB 和 FastBoot):**


官网: [https://developer.android.google.cn/studio/releases/platform-tools](https://developer.android.google.cn/studio/releases/platform-tools)


在里面下载适用于你电脑的工具, 然后解压即可. 下载到的内容至少应该包含 adb.exe 和 fastboot.exe


**下载你所需要的刷机包:**


常用的刷机包都是卡刷包(zip格式), 它可以通过 TWRP 直接进行安装, 也可以在电脑上通过 ADB 旁加载的方式进行安装.


下面介绍的通过 ADB 测加载的方式安装卡刷包的方式.


> 卡刷包的名称由来... 早期都是把刷机包存到存储卡里, 然后装系统的时候, 直接在 TWRP 中选择存储卡中的刷机包, 就可以安装, 除了刷入 TWRP 的过程, 其他过程都不需要电脑进行操作.


> 线刷包是直接通过 FastBoot 就能直接安装的刷机包, 不需要刷入 TWRP, 但是第三方系统是不会做这种刷机包的, 一般都是官方提供线刷包, 用来救砖(线刷包是基本不会失败的刷机方式)


## 刷入 TWRP

TWRP 全称 Team Win Recovery Project, 是一个开放源码软件的定制Recovery映像, 供基于安卓的设备使用. 它提供了一个支持触摸屏的界面, 有很多奇妙的小功能, 例如擦除手机数据, 安装系统, 备份系统, 通过旁加载让电脑 ADB 刷入系统什么的. 总而言之, 刷机必用. (Root 也是)


官网: [https://twrp.me/](https://twrp.me/)


你需要在 TWRP 的官网下载与自己设备对应的 TWRP 镜像 (img文件)


1. 手机进入 BootLoader (关机状态下, 长按 `音量-` 和 `电源` )
2. 连接手机与电脑


现在在电脑上使用命令行, 执行 `fastboot devices` 查看连接是否正常, 你的控制台应该输出类似于以下内容的信息:

```
hyxobieieaqgcinr        fastboot
```


左侧时手机名称, 右侧是手机当前所处于的状态.


3. 现在执行 `fastboot flash recovery twrp.img` 指令, 其中 `twrp.img` 应该是你下载下来的 TWRP 镜像文件路径.


如果成功刷入, 你的控制台应该输出类似于以下内容的信息:

```
Sending 'recovery' (65536 KB)                      OKAY [  1.670s]
Writing 'recovery'                                 OKAY [  0.538s]
Finished. Total time: 2.228s
```


执行完上述步骤之后, TWRP 就成功刷入到你的手机中了, 但是注意, TWRP 是可能会被你的手机系统重新覆盖掉, 如果此时你重启手机, 进入手机系统, 你的 TWRP 可能会丢的 ~~(MIUI 就喜欢干这事儿)~~


4. 执行 `fastboot reboot recovery` 以使手机直接重启到 recovery 中


如果成功进入, 你的屏幕上会显示 TEAMWIN 的字样 (样子怪怪的, 刚开始我是没认出来的)


## 清除手机数据

网上有着很多, 例如双清, 三清, 四清啥的, 其实就是清除几个分区的数据罢了. 具体安装系统的时候还是要以该系统安装的官方教程为准, 例如 Pixel Experience 只需要清除 Data 分区的数据即可


在 TWRP 主页面选择 清除(Wipe) 然后里面有默认的三清以及右侧的格式化 Data.


> 网上对于双清的说法也不同, 有的人说清除 Data + Cache, 有的人说清除 Cache + Dalvik, 笔者也不清楚具体怎么称呼, 总而言之, 按需清除. 大多数装系统都是清除 Data + Cache + Dalvik


如果要清除更多数据, 例如将 System 分区和 Internal Storage 分区清除, 使用左边的高级按钮就完事儿了.


> 我是个憨批,,, 这文章写着写着, 不小心就操作上了, 然后我手机数据就被我手贱, 清除掉了! (这么惨了, 还不点个赞...)


## 侧加载刷机包

现在, 在电脑打开命令行, 执行 `adb devices`, 如果设备连接正常, 则会显示以下内容:

```
List of devices attached
hyxobieieaqgcinr        recovery
```


在手机的 TWRP 主页面中, 点击左下角的 高级(Advanced), 再点击 ADB Sideload, 然后滑动滑块, 开始 Sideload


然后在电脑上执行 `adb sideload xxx.zip`, 其中 `xxx.zip` 是你下载的卡刷包.


等待完成, 然后重启手机即可 (手机上有 重启系统(Reboot System) 按钮, 也可以在电脑上执行 `adb reboot` 重启手机


重启完毕后, 如果一切操作没有问题, twrp 和刷机包都是与手机适配的, 那么你现在大抵是已经进入了系统了.


## Magisk 与 Root

Magisk（也被称作面具）是一套开放源代码的Android（5.0以上版本）自定义工具套组，内置了Magisk Manager（图形化管理界面）、Root、启动脚本、SElinux补丁和启动时认证/dm-verity/强制加密移除功能.


简而言之, 这玩意儿可以用来拿 Root 权限, 而且操作步骤极其简单.


1. 下载 Magisk 的安装包 (是一个 apk 文件)
2. 将 apk 文件复制出来一份, 后缀改为 zip
3. 手机进入 Recovery, 并准备 sideload
4. 电脑执行 `adb sideload magisk.zip`


没错, Magisk 的刷入, 和刷入系统一样, 都是通过侧加载进行的. (注意, 不需要清除数据)


操作完毕并重启后, 你的手机上就会多一个图标很奇怪的 Magisk 软件, 点击它, 它会提示你 "安装完整 Magisk" 啥的, 下一步就是直接在手机上安装 Magisk 这个软件了, 你可以直接拿刚刚的安装包用来安装, 当然更简单的方式是: 


5. 电脑执行 `adb install magisk.apk`, 然后手机就直接装上 Magisk 软件了.


在以上步骤完成之后, 你的手机就成功 Root 了, 别的软件可以请求 Root 权限, 而你需要的只是在 Magisk 内同意或者拒绝即可.


## 9008

这东西是仅骁龙处理器支持的刷机方式, 通过短接主板上的指定触点或者通过奇妙的深度刷机线短接两根线来进入对应模式


因为我还没逝过这个方式(我手机红米 Note 8 Pro, 是联发科的处理器), 所以这里就不讲了, 网上已经有完善的教程了.


## 绕过 FRP

如果你刷入的是国际版的手机系统, 并且它需要谷歌验证, 例如 Pixel Experience 这种类原生系统, 你可能会遇到无法验证的问题. 毕竟国内无法正常访问谷歌的服务.


这个问题是因为在之前的旧系统中, 有登陆谷歌账户, 并且在刷机前没有清除这个数据. 下面给出参考的解决方案:


- 手机连接电脑 WiFi 并使用电脑上的代理. (科学上网)
- 直接清空 FRP 数据, 只需要使用 adb, 执行 `adb shell` 进入手机命令行, 执行以下指令:


```bash
dd if=/dev/zero of=/dev/block/bootdevice/by-name/frp
dd if=/dev/zero of=/dev/block/bootdevice/by-name/config
```


- 在谷歌账户管理中, 找到自己登陆过谷歌账户的设备(当前手机), 然后退出账户.


> 以上方案任意一个都可以直接使用, 但是不保证所有手机都可以绕过. 例如我实测第三个方案是不能用的, 第二个方案可以直接使用, 但是有些别的用户反应, 在 Android 13 上需要使用方案三来解决.
> 方案一在我这里也没有通过测试, 但是理论上这个是完全可以使用的


> 如果在执行 `dd if=/dev/zero of=/dev/block/bootdevice/by-name/frp` 时执行失败, 可以加上额外的参数, 如下:
> `dd if=/dev/zero of=/dev/block/bootdevice/by-name/frp bs=512 count=1024`


## 总结

```mermaid
flowchat
st=>start: 开始
e=>end: 结束
bl=>operation: 解 BL 锁
blok=>condition: 成功?
ready=>operation: 准备工作(下载所需 TWRP 和刷机包)
twrp=>operation: 刷入 TWRP
wipe=>operation: 清除手机数据
sideload=>operation: 通过侧加载刷入刷机包

hasbl=>condition: 手机有 BL 锁?
st->hasbl
hasbl(yes)->bl->blok
hasbl(no)->ready
blok(yes)->ready
blok(no)->bl

ready->twrp->wipe->sideload->e
```
