---
title: 树莓派/centos开机自启python脚本
index_img: https://xinhaojin.github.io/imgs-host/past/2021/01/image-51.png
tags: []
id: '1377'
categories:
  - - 杂
date: 2021-02-15 21:25:39
---

    sudo vim /etc/rc.local

在exit 0前面加入一行，如图所示，main.py是python文件名，main.log是运行后将要生成的日志文件

![](https://xinhaojin.github.io/imgs-host/past/2021/01/image-51.png)

在centos7中，/etc/rc.d/rc.local的权限被降低了，所以需要执行如下命令赋予其可执行权限

    chmod +x /etc/rc.d/rc.local