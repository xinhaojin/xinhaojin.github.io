---
title: 树莓派4B连接WiFi
tags: []
id: '1263'
categories:
  - - 树莓派
date: 2020-12-30 00:33:50
---

新建TXT文件，格式如：

    country=CN
    ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
    update_config=1
    network={
        ssid="目标Wi-Fi名称"
        psk="目标Wi-Fi密码"
        priority=3
    }
    network={
        ssid="目标Wi-Fi名称"
        psk="目标Wi-Fi密码"
        priority=2
    }

ssid为WiFi名称，psk为密码，保留引号，priority为连接优先级

重命名为wpa_supplicant.conf，复制文件到boot目录下

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2020/12/image-22-1024x516.png)

拔掉网线重新启动，查看路由器，发现没有连上网，折腾了很久，发现好像需要手动打开WiFi开关

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2020/12/image-23.png)

重启，上线！

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2020/12/image-24-1024x493.png)

这时候有一个变化，就是IP改了，远程连接时需要进行修改。