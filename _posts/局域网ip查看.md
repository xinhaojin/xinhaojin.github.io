---
title: 局域网IP查看
tags: []
id: '1468'
categories:
  - - 杂
date: 2021-04-06 19:34:29
---

cmd命令窗口输入ipconfig,找到当前使用的网卡的IPv4地址

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/04/image.png)

记住前3个数，那么我们要找的局域网内其他设备的IP基本上就是192.168.54.xxx

输入for /L %i IN (1,1,254) DO ping -w 2 -n 1 192.168.54.%i，等待运行完成

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/04/image-2.png)

输入arp -a，查看局域网内其它设备的IP，其中192.168.54.xxx的，且物理地址不全为f的，就是要找的设备

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/04/image-4.png)

更好的方法

[列出局域网内可Ping通的IP](https://xinhaojin.github.io/2022/02/25/列出局域网内可ping通的ip/)