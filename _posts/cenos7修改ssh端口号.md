---
title: cenos7修改ssh端口号
index_img: https://xinhaojin.github.io/imgs-host/past/2021/04/QQ截图20210417012556.png
tags: []
id: '1514'
categories:
  - - 杂
date: 2021-04-17 10:19:15
---

以前真没想过会有人攻击我的服务器，所以也就没改ssh默认端口，直到我看到这个

![](https://xinhaojin.github.io/imgs-host/past/2021/04/QQ截图20210417012556.png)

我看了下，从4月13号开始，连续攻击了4天，滚犊子玩意儿，nmd，改端口！

打开ssh配置文件

    vim /etc/ssh/sshd_config

找到port，取消注释，改为自定义端口

重启ssh服务

    /etc/init.d/sshd restart

添加防火墙放行规则，如修改ssh端口为xxx,则

    iptables -I INPUT  -p tcp --dport xxx -j ACCEPT

有宝塔面板的记得在安全组中放行对应端口，有云服务器供应商提供的控制台的也在安全组中放行对应端口，顺便把默认22端口禁了

Done.