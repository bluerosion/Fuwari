---
title: re：从零开始的qqbot搭建指南（part1）
published: 2026-03-16
description: 此指南将分为两部分教你如何从零开始选择并搭建你想要的qq机器人
image: ../assets/images/
tags:
  - bot
category: qqbot搭建指南
draft: false
lang: zh_CN
---
# RE：从零开始的QQBot搭建指南（part-1）

新人初稿，写的不好请见谅

## 前言

最近总有人问我我的bot是如何搭建的，虽然网上有很多相关教程，但自己写一篇不是更好owo
## 什么是QQ协议端

此框架`通常`为修改qq客户端或模拟qq协议（协议端），将qq的功能以特定的协议暴露出来，通常可以通过其它框架（如koishi，astrbot）对接其使用的协议（通常为onebot）

### NapCat（现代最强！）
![[../assets/images/qqbot-part1_1.webp]]
#### 什么是Napcat？

按照官方的话来说就是：
> 基于 TypeScript 构建的 Bot 框架，通过相应的启动器或者框架，主动调用 QQ Node 模块提供给客户端的接口，实现 Bot 的功能。

通俗点开讲就是将qq的接口按照某种协议（即onebot）
### Lagrange（历史最强！）
（没找到logo，凑合看吧）

### Mirai及其衍生框架（我去，老资历！）
![[../assets/images/qqbot-part1_2.png]]
（图中为Mirai衍生框架go-cqhttp）
