---
title: Re：从零开始的QQ机器人搭建指北
published: 2026-04-07
description: 我将教你如何从零搭建一个QQ机器人
image: ./assets/images/qqbot-part1.jpg
tags:
  - bot
category: Re：从零开始的QQ机器人搭建指北
draft: false
lang: zh_CN
---
新人初稿，写的不好请见谅

万万没想到这东西我居然拖了两个多月，我还是太有实力了（划掉）

# 前言

本文仅代表作者个人观点，请勿污染社区环境！

# QQ协议端

**如想直接查看部署教程，可跳过该章节**

此框架通常为修改qq客户端或模拟qq协议，将qq的功能以特定的协议暴露出来，通常可以通过其它框架（如koishi，astrbot）对接其使用的协议（通常为OneBot）

**这些协议端在官方定义里也是bot框架，但大部分无法加载通过官方途径加载第三方插件，但都具备以上特征，为了方便区分，统称为协议端**

## NapCat
![[qqbot-part1-1.png]]

> 基于 TypeScript 构建的 Bot 框架，通过相应的启动器或者框架，主动调用 QQ Node 模块提供给客户端的接口，实现 Bot 的功能。
>  ——摘自官方文档

相较于下面要讲的Langrange项目，NapCat选择了直接调用QQNT的底层node模块，这也解决了Lagrange的一大痛点：依赖线上签名服务器。

但是NapCat运行时需要运行一个完整的QQ，当机器人接入过多的群聊时会出现内存占用过大的弊端，这个弊端同时也是目前所有采用本地签名的框架逃不开的问题（如LLBot，鉴于篇幅原因，这里不再赘述）

## Lagrange

![[qqbot-part1-2.jpeg]]

> Lagrange为该项目名称，该项目中的框架较出名的有：Lagrange.OneBot、go-cqhttp（此项目与Mirai版虽然同名，但实现技术完全不同！不要搞混了！）

Lagrange的所有项目均基于其内核Lagrange.core，相较于mirai这类模拟安卓qq协议的框架不同，Lagrange模拟的是QQNT协议，由于其是模拟相关协议，所以也继承了mirai这类框架内存占用小的优点，但缺点也很明显，极度依赖线上签名服务器

**由于部分原因，Lagrange项目已全部停更，官方签名服务器已关闭，各位可以使用Napcat等本地签名的QQNT框架，如想继续使用Lagrange框架，请自备签名服务器，这里仅介绍相关bot框架**

# mirai及其衍生框架
![[qqbot-part1-3.png]]

> Mirai，正如你所见，这个名字不带任何的前缀或后缀，它是整个生态的中心，在这个生态中，所有的项目都直接或间接与 Mirai 有密不可分的关系，Mirai为用户提供了最基础且核心的功能：接收消息与发送消息。
>  ——摘自官方文档

与酷Q同时代的产物，2020年酷Q停运后（根据网络上搜到的消息，Mirai貌似也有停运的消息），Mirai替代了酷Q，2023年，随着tx对协议库的围追堵截，Mirai停止了开发

酷Q（CKYU）
![[qqbot-part1-4.jpg]]

由Coxss等人主导开发，其衍生插件协议CQHTTP（酷Q停运后由社区成员接手修改为OneBot协议）至今仍在被各大框架采用，2020年晨风机器人作者被捕后酷Q等机器人框架停止运营，结束了酷Q九年来的辉煌...

由于作者能力有限，再加上入坑时间较晚，我明白还有比这更古老的框架......但是我已无力考证，更古早的要不被tx紫菜了，要不已经被时间的冲刷下变的无法寻找，我已经尽力寻找酷Q的相关内容，如有缺失，欢迎各位补充

# 部署指南

## NapCat

**此教程~~搬运~~修改自[NapCat文档](https://napneko.github.io/guide/napcat)**

### shell版部署教程
#### Windows
##### 手动启动
1. 前往[NapCatQQ的github Release页面](https://github.com/NapNeko/NapCatQQ/releases)下载NapCat.Shell.zip并解压
2. 确保 QQ 版本安装且最新
3. 双击目录下 launcher.bat 即可启动 如果是 Win10 则使用 launcher-win10.bat
4. 如果需要快速登录 将 QQ 号传入参数即可（launcher.bat 123456），或者后续在NapCatWebui中配置
##### 一键版本

**特殊说明: 一键版仅适用 Windows.AMD64 无需安装 QQ 和 NapCat 已内置**

1. 前往[NapCatQQ的github Release页面](https://github.com/NapNeko/NapCatQQ/releases)下载 NapCat.Shell.Windows.OneKey.zip 无头绿色版本解压
2. 点击 NapCatInstaller.exe 等待自动化配置
3. 进入 NapCat.XXXX.Shell 目录
4. 启动 napcat.bat

Napcat也有一个Windows版可视化工具，由于作者没用过，在此不再赘述

#### Linux

##### 一键部署脚本
支持Ubuntu 20+/Debian 10+/Centos
命令行运行

```bash
curl -o
napcat.sh
https://nclatest.znin.net/NapNeko/NapCat-Installer/main/script/install.sh
&& bash napcat.sh
```

##### Docker

NapCat也支持Docker部署

仓库地址：https://github.com/NapNeko/NapCat-Docker

在此之前，请先确保你已安装过Docker
#### 命令行运行

```bash
docker run -d
-e NAPCAT_GID=$(id -g)
-e NAPCAT_UID=$(id -u)
-p 3000:3000
-p 3001:3001
-p 6099:6099
--name napcat
--restart=always
mlikiowa/napcat-docker:latest
```
#### docker-compose 运行

```yaml
version: "3"
services:
    napcat:
        environment:
            - NAPCAT_UID=${NAPCAT_UID}
            - NAPCAT_GID=${NAPCAT_GID}
        ports:
            - 3000:3000
            - 3001:3001
            - 6099:6099
        container_name: napcat
        network_mode: bridge
        restart: always
        image: mlikiowa/napcat-docker:latest
```

### Framework版部署教程

**请注意！自 QQ 9.9.19 后 LiteLoaderQQNT 维护欠缺，NapCatQQ 鼓励用户迁移到 Shell 版本，其官方的修补方式与安装方式不再推荐。**

#### Windows

##### NapCat.Win.一键版本

**一键版仅适用 64位Windows，已内置 QQ 和 NapCat**

1. 前往[NapCatQQ的github Release页面](https://github.com/NapNeko/NapCatQQ/releases)下载NapCat.Shell.zip并解压
2. 选择 NapCat.Framerwork.Windows.Once.zip 下载
3. 找到目录下的 exe 启动 (注意不要解压到带有空格或者中文的目录)

NapCat.Framework - 通用性手动教程

按照 [LiteLoaderQQNT 官网](https://liteloaderqqnt.github.io/) 的指导安装 LiteLoaderQQNT 框架。

在 LiteLoaderQQNT 的设置页面（如下图）将 NapCat.Framework.zip 导入即可。

![[qqbot-part1-5.png]]

**强烈不推荐 LL 官方的修补方案，其方案将导致 NapCat 扩展 API 失效的。 同时污染 QQ 本身环境，添加环境变量，清理 LL 需要一定计算机基础。 包括需要调试 QQ 的用户，强烈推荐 once 或者绿色版本。**

#### Linux

##### NapCat.Docker.Framework - Linux 容器化部署

```bash
docker run -d
-e VNC_PASSWD=vncpasswd
-p 5900:5900
-p 6081:6081
-p 3000:3000
-p 3001:3001
-p 6099:6099
--name napcatf
--restart=always
mlikiowa/napcat-framework-docker:latest
```

##### NapCat.Installer - Linux 一键部署脚本

```bash
curl -o napcat.sh https://nclatest.znin.net/NapNeko/NapCat-Installer/main/script/install.framework.sh && bash napcat.sh
```

## Langrage

**此教程~~搬运~~修改自[Langrage.OneBot文档](https://lagrangedev.github.io/Lagrange.Doc/v1/Lagrange.OneBot)**

### 下载安装

下载 Lagrange.OneBot 可执行文件并解压

1. 可以从 [Releases](https://github.com/LagrangeDev/Lagrange.Core/releases) 下载对应系统版本 (该版本~~可能不~~为最新版本)
2. 还可以从 [Actions](https://github.com/KonataDev/Lagrange.Core/actions/workflows/Lagrange.OneBot-build.yml) 中获得当前最新的构建

**注意！从 Actions 下载的 Lagrange.OneBot 需要手动安装 .Net, 推荐从 [Releases](https://github.com/LagrangeDev/Lagrange.Core/releases) 下载对应系统版本你应当自行安装对应版本的 [.Net SDK](https://dotnet.microsoft.com/zh-cn/download)**

### 运行

#### 对于windows
```
双击 Lagrange.OneBot.exe 运行即可
```

#### 对于Linux/MacOS
```shell
# 可执行权限（可选）
chmod +x ./Lagrange.OneBot
# 运行
./Lagrange.OneBot
```

第一次运行时, 会在同级目录下自动生成默认的 `appsettings.json` 配置文件, 你可以在[这里](https://lagrangedev.github.io/lagrange-config-generator/)生成配置文件并用生成的内容**覆盖**原本的 `appsettings.json`，或按照下文的指导正确[修改配置文件](#配置文件)以设置 Lagrange.

在配置文件按需修改后（推荐使用扫码登录）, 在命令行中按任意键, Lagrange 将正式运行在同一文件夹下会出现一张登录二维码图片 qr-0.png, 在二维码过期前尽快使用手机 QQ 扫码连接

**tips：手机扫描登录二维码时, 推荐勾选「下次登录无需确认」**

**注意：NTQQ 的 SignServer 不可与 Android 协议混用（如 unidbg-fetch-qsign）**

### 配置文件

[Lagrange Config Generator](https://lagrangedev.github.io/lagrange-config-generator/) 简化了配置文件的生成过程. 你也可以参考下文的说明手动修改配置文件.

```json5{11-12,24-43}
{
  "$schema": "https://raw.githubusercontent.com/LagrangeDev/Lagrange.Core/master/Lagrange.OneBot/Resources/appsettings_schema.json",
  "Logging": {
    "LogLevel": {
      "Default": "Information",  // 提 Issue 时请切换到 Trace
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information",
    },
  },
  "SignServerUrl": "https://sign.lagrangecore.org/api/sign",
  "SignProxyUrl": "", //留空不使用代理,仅支持http代理, example: http://127.0.0.1:7890
  "MusicSignServerUrl": "",
  "Account": {
    "Uin": 0,  // 用于识别 db 和 qrcode 文件, 无任何其他用途
    "Password": "",  // 不再支持
    "Protocol": "Linux",  // 使用 Linux 协议
    "AutoReconnect": true,
    "GetOptimumServer": true,
  },
  "Message": {
    "IgnoreSelf": true,  // 忽略 Bot 自身的消息
    "StringPost": false,
  },
  "QrCode": {
    "ConsoleCompatibilityMode": false,
  },
  "Implementations": [  // 服务实现 支持多链接
    {
      "Type": "ReverseWebSocket",
      "Host": "127.0.0.1",
      "Port": 8080,
      "Suffix": "/onebot/v11/ws",
      "ReconnectInterval": 5000,
      "HeartBeatInterval": 5000,
      "HeartBeatEnable": true,
      "AccessToken": "",
    },
    {
      "Type": "ForwardWebSocket",
      "Host": "127.0.0.1",
      "Port": 8081,
      "HeartBeatInterval": 5000,
      "HeartBeatEnable": true,
      "AccessToken": "",
    },
  ],
}
```

**注意：以 `//` 开头的为注释, 试图复制粘贴到实际的配置文件中时务必删除**

服务实现目前支持以下多种方式, 请根据需要添加到配置文件中的 `Implementations` 中

- [快速部署 \& 配置](#快速部署--配置)
  - [下载安装](#下载安装)
  - [运行](#运行)
  - [配置文件](#配置文件)
    - [反向 WebSocket 配置](#反向-websocket-配置)
    - [正向 WebSocket 配置](#正向-websocket-配置)
    - [HTTP POST 配置](#http-post-配置)
    - [正向 HTTP 配置](#正向-http-配置)
  - [关于验证码](#关于验证码)

#### 反向 WebSocket 配置

```json5
{
	"Type": "ReverseWebSocket",
	"Host": "127.0.0.1",
	"Port": 8080,
	"Suffix": "/onebot/v11/ws",
	"ReconnectInterval": 5000,
	"HeartBeatInterval": 5000,
	"HeartBeatEnable": true,
	"AccessToken": ""
}
```

#### 正向 WebSocket 配置

```json5
{
	"Type": "ForwardWebSocket",
	"Host": "127.0.0.1",
	"Port": 8081,
	"HeartBeatInterval": 5000,
	"HeartBeatEnable": true,
	"AccessToken": ""
}
```

#### HTTP POST 配置

```json5{3}
{
  "Type": "HttpPost",
  "Host": "127.0.0.1", // 可以填写前缀协议, 例如 `https://`
  "Port": 8082,
  "Suffix": "/",
  "HeartBeatInterval": 5000,
  "HeartBeatEnable": true,
  "AccessToken": "",
  "Secret": ""
}
```

**注意：以 `//` 开头的为注释, 试图复制粘贴到实际的配置文件中时务必删除**

**如果出现验证码登录的情况 推荐删除 `Keystore` 进行扫码登录，验证码登录因为未知原因成功率较低**

## go-cqhttp 

**此处特指Mirai衍生框架**

**此教程~~搬运~~修改自[go-cqhttp文档](https://docs.go-cqhttp.org/guide/quick_start.html#%E5%9F%BA%E7%A1%80%E6%95%99%E7%A8%8B)**

### 基础教程

#### 下载

从 [release](https://github.com/Mrs4s/go-cqhttp/releases) 界面下载最新版本的 go-cqhttp

| 系统类型          | 可执行文件                         | 压缩文件                            |
|:-------------:|:-----------------------------:|:-------------------------------:|
| Intel 版 Macos | Not available                 | `go-cqhttp_darwin_amd64.tar.gz` |
| M1 版 Macos    | Not available                 | `go-cqhttp_darwin_arm64.tar.gz` |
| 32 位 Linux    | Not available                 | `go-cqhttp_linux_386.tar.gz`    |
| 64 位 Linux    | Not available                 | `go-cqhttp_linux_amd64.tar.gz`  |
| arm64 Linux   | Not available                 | `go-cqhttp_linux_arm64.tar.gz`  |
| armv7 Linux   | Not available                 | `go-cqhttp_linux_armv7.tar.gz`  |
| 32 位 Windows  | `go-cqhttp_windows_386.exe`   | `go-cqhttp_windows_386.zip`     |
| 64 位 Windows  | `go-cqhttp_windows_amd64.exe` | `go-cqhttp_windows_amd64.zip`   |
| arm64 Windows | `go-cqhttp_windows_arm64.exe` | `go-cqhttp_windows_arm64.zip`   |
| armv7 Windows | `go-cqhttp_windows_armv7.exe` | `go-cqhttp_windows_armv7.zip`   |


- SHA-256信息在 `go-cqhttp_checksums.txt` , 可用于校验文件完整性
- 如果没有你所使用的系统版本或者希望自己构建, 请移步 [进阶指南-如何自己构建](#如何自己构建)

#### 解压

- Windows下请使用自己熟悉的解压软件自行解压
- Linux下在命令行中输入 `tar -xzvf [文件名]` 

#### 使用

##### Windows 标准启动方法

1. 双击`go-cqhttp_*.exe`，根据提示生成运行脚本
2. 双击运行脚本
```
[WARNING]: 尝试加载配置文件 config.yml 失败: 文件不存在
[INFO]: 默认配置文件已生成,请编辑 config.yml 后重启程序.
```
3. 参照[config.md](https://github.com/ishkong/go-cqhttp-docs/blob/main/docs/guide/config.md)和你所用到的插件的 `README` 填入参数
4. 再次双击运行脚本
```
[INFO]: 登录成功 欢迎使用: balabala
```

如出现需要认证的信息, 请自行认证设备。

此时, 基础配置完成

##### Linux 标准启动方法

1. 通过 SSH 连接到服务器
2. `cd`到解压目录
3. 输入 `./go-cqhttp`, `Enter`运行 , 此时将提示
```
[WARNING]: 尝试加载配置文件 config.yml 失败: 文件不存在
[INFO]: 默认配置文件已生成,请编辑 config.yml 后重启程序.
```

4. 参照 [config.md](https://github.com/Mrs4s/go-cqhttp/blob/master/docs/config.md) 和你所用到的插件的 `README` 填入参数
5. 再次输入 `./go-cqhttp`, `Enter`运行
```
[INFO]: 登录成功 欢迎使用: balabala
```

如出现需要认证的信息, 请自行认证设备。

此时, 基础配置完成

**注意：需要保持 go-cqhttp 在后台持续运行，请配合 screen 等服务来保证断开 SSH 连接后 go-cqhttp 的持续运行**

##### 开始与 go-cqhttp 交互

在 `go-cqhttp` 成功运行之后, 你就可以通过自己写的程序, 使用 `HTTP` 或者 `WebSocket` 与 `go-cqhttp` 进行通讯, 实现 QQ 机器人. 在这个过程中, 你的程序只需要把一些数据发送给 `go-cqhttp`, 关于 QQ 的通信协议, `go-cqhttp` 会帮你解决.

你也可以将 `go-cqhttp` 部署在你的服务器上, 并加上验证, 这样, 你就可以在各个地方使用自己的程序连接到远程的 `go-cqhttp`.

想要与 `go-cqhttp` 进行通信, 你可以使用专门为 `go-cqhttp` 开发的开源 SDK, 这是最好的选择. 不过你也可以直接使用实现 `OneBot` 协议的开源 SDK, 只是他们可能并不包含 `go-cqhttp` 中的某些 API 或者消息类型, 这或许会影响你的使用体验.

##### 已知的 go-cqhttp 通信 SDK

| 语言 / 平台 | 名称 | 简介 | 通信协议支持 | 开源 |
| --- | --- | --- | --- | --- |
| C# / .NET | EleCho.GoCqHttpSdk | 完全遵守 C# 命名规范, 优雅与便捷的 Go-CqHttp 通信 SDK, 完全支持 array 与 string 通信格式 | 正反向 HTTP, 正向 WebSocket | [GitHub](https://github.com/OrgEleCho/EleCho.GoCqHttpSdk) |
| C# / .NET | IlyfairyLib.GoCqHttpSdk | 一个 go-cqhttp 的 C# SDK, 使用了类似管道的模式 | 正向 HTTP, 正向 WebSocket | [GitHub](https://github.com/ilyfairy/IlyfairyLib.GoCqHttpSdk) |
| C# / .NET | Saladim.QBot | 封装了大部分常用事件及接口并从其抽象, 从抽象层再次封装了简单可依赖注入的简单服务(指令解析, 事件处理管线, 简单的协程, session等) | 正反向HTTP, 正向WebSocket | [Github](https://github.com/saladim-org/Saladim.QBot) |
| GO | leafbot | 基于 go-cqhttp，实现 onebot 协议的 go 语言版本 sdk，拥有内置插件。| 正向 HTTP, 正反向 WebSocket| [GitHub](https://github.com/huoxue1/leafbot) |
| GO | Nyabot | NyaBot 是一个基于 Go 语言的开源聊天机器人框架，支持多种聊天平台协议。| 正向 HTTP, 正反向 WebSocket | [GitHub](https://github.com/Elyart-Network/NyaBot) |
| PHP | 虞灪 | go-cqhttp的php sdk，拥有完整的代码提示和注释，使你可以脱离文档来快速开发 | 正向 HTTP | [GitHub](https://github.com/MicroDreamTeam/go-cq-http-sdk) |
| Python | pycqBot | go-cqhttp python 框架，可以用于快速搭建 bot | 正向 HTTP, 正向 WebSocket | [GitHub](https://github.com/FengLiuFeseliud/pycqBot) |
| Python | Nakuru Project | 一款为 go-cqhttp 的正向 WebSocket 设计的 Python SDK，支持纯 CQ 码与消息链的转换处理 | 正向 HTTP, 正向 WebSocket | [GitHub](https://github.com/Lxns-Network/nakuru-project) |


> 以上数据来自于 GitHub 搜索, 如有错误, 请指出

#### 跳过启动的五秒延时

使用命令行参数 `faststart`即可跳过启动的五秒钟延时，例如

```shell
# Windows
.\go-cqhttp.exe -faststart

# Linux
./go-cqhttp -faststart
```

### 进阶指南

#### 如何自己构建

1. [下载源码](https://github.com/Mrs4s/go-cqhttp/archive/master.zip)并解压 || 使用 `git clone https://github.com/Mrs4s/go-cqhttp.git` 来拉取

2. [下载golang binary release](https://golang.google.cn/dl/)并安装或者[自己构建golang](https://golang.google.cn/doc/install/source)

3. 在 `cmd` 或Linux命令行中, `cd` 到目录中

4. 输入 `go build -ldflags "-s -w -extldflags '-static'"`, `Enter` 运行

*注：可以使用* `go env -w GOPROXY=https://goproxy.cn,direct` *来加速国内依赖安装速度*

#### 更新

##### 方法一

从 [release](https://github.com/Mrs4s/go-cqhttp/releases) 界面下载最新版本的 go-cqhttp
并替换之前的版本

##### 方法二

使用更新参数, 在命令行中打开 go-cqhttp 所在目录

**windows**

输入指令
```powershell
go-cqhttp.exe update
```

如果在国内连接github下载速度可能很慢, 可以使用镜像源下载

```powershell
go-cqhttp.exe update https://hub.fgit.ml
```

目前可用的镜像源
- `https://hub.fgit.ml`

**linux**

方法与windows基本一致, 将 `go-cqhttp.exe` 替换为 `./go-cqhttp` 即可

```shell
./go-cqhttp update
```

### 安装 ffmpeg

为了支持任意格式的语音发送, 你需要安装 ffmpeg 。

#### Windows

从 [这里](https://www.gyan.dev/ffmpeg/builds/ffmpeg-release-full.7z) 下载 并解压, 并为 `bin` 这个文件夹添加环境变量。

如果遇到下载速度缓慢的问题可以用 [这个源](https://downloads.go-cqhttp.org/ffmpeg-release-full.7z) 。

然后在 cmd 输入 **(不能使用 powershell）**

```shell
setx /M PATH "C:\Program Files\ffmpeg\bin;%PATH%"
```

自行将这个指令中的 `C:\Program Files` 替换成你的解压目录。

#### Linux

Ubuntu / Debian :

终端执行

```shell
apt install -y ffmpeg
```

Fedora / RHEL / CentOS : 

根据 [Rpmfusion](https://rpmfusion.org/Configuration) 的文档配置源

终端执行

```shell
# Centos7 及之前
yum install ffmpeg ffmpeg-devel 

# CentOS8 及之后
dnf install ffmpeg ffmpeg-devel
```

回望过去的几年，QQ机器人框架可谓是命运多舛，如今tx依旧在增强检测手法，而它所谓QQ机器人，或许可以把它的旧官网标题反过来读，原标题“开元·合作·共赢”，反过来怎么读出于某些原因我这就不写了

~~近期tx给qq机器人开放了一堆权限，为了啥好难猜啊~~

**愿你前行的道路，群星闪耀。**
**愿你走过的旅途，百花绽放。**
**你即是上帝的馈赠；世界因你而瑰丽。**