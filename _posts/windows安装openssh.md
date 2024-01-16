---
title: windows安装openssh
tags: []
id: '1740'
categories:
  - - 软件安装配置
date: 2021-05-22 15:46:39
---

#### 不吐不快（有戾气，未成年人请在家长陪同下阅读）

就装个SSH，网上一堆教程贼xx，真不知道哪里来的自信发出来，没图没逻辑没重点没上下文，我吐了

有些人真以为自己安装成功了就可以随便发几句话出来成为小白教程了？傻逼。

#### 下载

[https://github.com/PowerShell/Win32-OpenSSH/releases](https://github.com/PowerShell/Win32-OpenSSH/releases)

![](https://xinhaojin.github.io/imgs-host/past/2021/05/image-35.png)

#### 安装

解压到c:\\Program Files\\

![](https://xinhaojin.github.io/imgs-host/past/2021/05/image-36.png)

在OpenSSH-Win64文件夹中打开cmd

    powershell.exe -ExecutionPolicy Bypass -File install-sshd.ps1
    sc config sshd start= auto
    net start sshd

![](https://xinhaojin.github.io/imgs-host/past/2021/05/image-37.png)

添加环境变量

环境变量-系统变量-Path末尾添加;`C:\Program Files\OpenSSH-Win64`

#### 备注

以上安装只针对windows作为客户端的情况