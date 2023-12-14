---
title: '[Unity] 碰撞器, 触发器, 刚体,Dynamic, Kinematic, Static, OnCollision, OnTrigger 全讲'
slug: '[Unity]碰撞器,触发器,刚体,Dynamic,Kinematic,Static,OnCollision,OnTrigger全讲'
date: 2022-09-27 14:51:27
tags:
  - unity
  - csharp
  - 游戏引擎
categories:
  - dotnet
  - Unity
description: 'Unity 碰撞相关的几乎所有基础知识都在这里了, 高度整合以及精心标注!'
---

详细的讲解关于 Unity 中碰撞的各种细节, 文章以 Unity2D 为主讲起, 并且附上关于 Unity3D 的相关介绍


> 文章长期更新, 也欢迎评论区进行纠正或补充
> 另外, 如果你遇到了一些问题, 建议看完整篇文章, 在末尾有一些常见小问题的标注


## 碰撞器 / Collider

碰撞器是最基本的用来检测碰撞的玩意儿, 例如你要做物理效果, 需要一个墙, 一个球扔过去, 墙挡住这个球, 这个过程中就需要判断墙与球是否碰撞


碰撞器的类型有很多, 例如 BoxCollider, CircleCollider 什么的, 但是用途都一样, 只不过检测的范围形状不一样罢了


## 触发器 / Trigger

碰撞器中有一个 IsTrigger 属性, 可以设定当前的碰撞器是否是 "触发器", 如果设定了 true, 也就是当一个碰撞器是触发器时, 那么这个物体就不再参与任何物理碰撞.


例如, 墙的碰撞器设置了 IsTrigger, 那么这个墙就不能再挡住一个球.


> 虽然设置了触发器的游戏物体不参与物理碰撞, 但是它仍然有物理效果, 例如你可以设置他的速度, 重量, 什么的, 这些仍然是有效的


## 刚体 / RigidBody

刚体是 Unity 中实现物理效果的最基本组件, 例如加了刚体的游戏对象可以受重力影响, 两个加了刚体的游戏对象再相撞时, 会像显示一样, "阻拦", 而不是穿过彼此.


刚体依赖于碰撞器, 当你添加刚体组件的时候, 他也会默认添加一个碰撞器, 如果你需要别的碰撞器, 可以自己手动更改


刚体中有最为重要的一个选项 "Body Type", 它的值可以是 动态的(Dynamic), 运动学的(Kinematic), 静态的(Static)


在下面介绍各种类型的刚体时, "是否产生碰撞"表示是否会触发 "OnCollision" 系列的事件. "受影响" 标识该刚体是否会受目标刚体的力的影响.


例如, Dynamic 会被 Kinematic 刚体阻拦, Dynamic 也可以被 Kinematic 撞飞, 但是 Kinematic 不会受 Dynamic 刚体的影响, 所以 Kinematic 不可能被 Dynamic 撞飞


另外, 如果 A 刚体不会穿过 B 刚体, 那么说明 B 刚体对 A 产生影响, 也就是说, 当 A 刚体静止时, B 刚体以一定速度冲向 A, 那么可能


> Unity 3D 中没有 Static 刚体.


## 动态的 / Dynamic

当一个刚体的 Body Type 是 Dynamic 时, 这个游戏对象拥有全部的物理效果, 包括重力, 游戏对象之间的作用力. 他会与其他的所有刚体有物理碰撞效果, 并且能够与其他所有触发 "OnCollision" 系列事件.


在当前游戏对象为动态刚体时:


| 另一对象刚体类型 | 是否产生碰撞 | 受影响 | 原因 |
| --- | --- | --- | --- |
| Dynamic | O | O | 动态的刚体受一切刚体的影响, 具有所有物理特性 |
| Kinematic | O | O | 同上 |
| Static | O | O | 同上 |


> 在 Unity 3D 中,  一个刚体的默认行为就是 Dynamic


## 运动学的 / Kinematic


当一个刚体的 Body Type 是 Kinematic 时, 这个游戏对象仅拥有一部分物理效果, 并且消耗比动态刚体更少的性能.


它不会受重力或其他任何力影响, 你可以理解为它拥有无限的质量, 其他任何游戏对象撞击它的时候, 都不会动摇他的位置.


而且, 不会与其他 Kinematic 刚体产生物理碰撞, 因为 Kinematic 本身就不是为碰撞而设计的, 它更适合做墙或者需要用脚本手动操作的游戏对象, 当然也不会触发 "OnCollision" 系列事件.


在当前游戏对象为运动学刚体时:


| 另一对象刚体类型 | 是否产生碰撞 | 受影响 | 原因 |
| --- | --- | --- | --- |
| Dynamic | X | X | Kinematic 只会横冲直撞, 速度, 角动量不会受任何影响 (参考三体中的水滴) |
| Kinematic | O | X | 同上 |
| Static | O | X | 同上 |


> 在 Unity 3D 中, 勾选 IsKinematic 选项来使当前刚体作为运动学刚体


## 静态的 / Static

静态的刚体不应该被移动, 或者说它的用途也是很少的, 因为它只会与 Kinematic 刚体产生碰撞. 在游戏中, 它应该作为墙, 或者地面.



在当前游戏对象为静态刚体时:


| 另一对象刚体类型 | 是否产生碰撞 | 受影响 | 原因 |
| --- | --- | --- | --- |
| Dynamic | X | X | 你的小篮球还能把地球撞飞不成 |
| Kinematic | O | X | Static 刚体只能与 Dynamic 2D 刚体碰撞 |
| Static | X | X | 因为这种刚体不是为了移动而设计的 |


## 碰撞事件 / OnCollision

两个刚体在碰撞的时候, 可以产生碰撞事件, 具体哪些对象可以产生碰撞事件, 在上面已经说了.


需要注意的是, 一旦产生碰撞事件, 双方都会触发 OnCollision 的 Unity 消息. 而且所传入的 Collision 参数也是不一样的. 但是无论怎样, Collision 的 collider 属性都是与当前碰撞的碰撞体实例 (otherCollider 属性一般很少用到)


碰撞事件有下面这些


| 方法名 | 描述 |
| --- | --- |
| OnCollisionEnter / OnCollisionEnter2D | 当该碰撞体/刚体已开始接触另一个刚体/碰撞体时，调用 OnCollisionEnter |
| OnCollisionStay / OnCollisionStay2D | 对于接触另一个碰撞体或刚体的每个碰撞体或刚体，每帧调用一次 OnCollisionStay |
| OnCollisionExit / OnCollisionExit2D | 当该碰撞体/刚体已停止接触另一个刚体/碰撞体时，调用 OnCollisionExit |



## 触发事件 / OnTrigger

使用触发器, 并且要引发触发事件, 必须保证: 两个互相碰撞的物体均包含碰撞器, 并且至少有一个碰撞器设置了 IsTrigger 为 true, 至少其中一个必须包含非静态(Static)刚体


触发事件有下面这些


| 方法名 | 描述 | 作者的大白话 |
| --- | --- | --- |
| OnTriggerEnter / OnTriggerEnter2D | GameObject 与另一个 GameObject 碰撞时，Unity 会调用 OnTriggerEnter | 俩游戏对象开始碰撞的时候调用 |
| OnTriggerStay / OnTriggerStay2D | 在另一个对象位于附加到该对象的触发器碰撞体之内时, 每帧调用一次 | 只要俩碰撞体还处于碰撞的状态, 每一帧就调用一次 |
| OnTriggerExit / OnTriggerExit2D | 当另一个对象离开附加到当前对象的触发器碰撞体时调用 | 俩游戏对象不再激情碰撞了就调用 |


> 至于网上所讲的, 两物体都有碰撞器, 并且其中一个是刚体, 是因为 Unity3D 中没有静态刚体, 所以只要有一个刚体就能触发. 而在 2D 中, 静态的刚体并不会引发触发器事件.


## 标注

1. 3D 的事件与 2D 的事件是不能通用的, 如果你是 2D 游戏对象, 并且使用的 2D 碰撞器与刚体, 那么使用 OnCollision 和 OnTrigger 什么的方法时, 必须使用带有 2D 后缀的方法
2. 需要注意的是, 例如一个地面, 只有一个碰撞体而不包含任何刚体, 一个带有碰撞体和刚体的游戏对象可以站立在这个地面上, 并且能够正常的触发碰撞相关的事件.
3. 如果你在做一个地刺效果, 玩家站立时不断受到伤害, 但是 OnTriggerStay 可能不会一直触发, 默认是在 Enter 的 0.5s 后可以持续触发, 然后进入 "Sleep" 状态, 此时除非玩家移动, 才会再次触发 TriggerStay 的消息. 如果你不希望它进入睡眠状态, 那么就把对应的刚体中, Sleep mode 改为 Never sleep. 如果你要全局更改这个时间, 可以在 Project Settings 中的 Physics2D 中, 将 Time to Sleep 改为你想要的值.


> 关于此文章任何错误信息或者有不足的地方, 欢迎评论区指正
