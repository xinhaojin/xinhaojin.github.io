---
title: 甲骨文云（Oracle Cloud）nginx启动失败（端口被占用）
tags: []
id: '1520'
categories:
  - - 杂
date: 2021-04-24 19:06:09
---

甲骨文云创建实例后，始终有进程占用80端口，导致nginx无法正常启用默认80端口，非常不方便

![](https://xinhaojin.github.io/imgs-host/past/2021/04/image-25.png)

当然可以kill之后再尝试，但是这明显是开启自启的服务，kill一次，还会有千千万万次

对于centos,终端输入ntsysv,查看所有服务

![](https://xinhaojin.github.io/imgs-host/past/2021/04/image-26.png)

方向键选择，找到相关的，按空格设置或取消服务，按tanab键切到下方按钮

这里明显需要关闭红框中的服务,也可以直接用命令

    systemctl stop oracle-cloud-agent
    systemctl disable oracle-cloud-agent
    systemctl stop oracle-cloud-agent-updater
    systemctl disable oracle-cloud-agent-updater

对于Ubuntu/Debian，使用service --status-all查看服务