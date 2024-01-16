---
title: 树莓派4B查找IP，开启SSH或VNC连接
tags: []
id: '1243'
categories:
  - - 树莓派
date: 2020-12-29 23:49:46
---

### 一、查找IP

方法有很多，只介绍我实践成功的方法，可能也是最方便的方法：

网线连接路由器和树莓派，树莓派网线接口灯光快闪表示连接正常，此时打开路由器终端管理界面：

在电脑连接WiFi的情况下，命令行运行ipconfig，无线局域网适配器的默认网关就是路由器终端管理界面地址

![](https://xinhaojin.github.io/imgs-host/past/2020/12/image-7.png)

![](https://xinhaojin.github.io/imgs-host/past/2020/12/image-8-1024x588.png)

找到有线连接设备，很多路由器在这里会显示Raspberry，很好识别，我这里因为设备少也很好识别，如果有多个疑似IP的话，可以挨个尝试SSH连接

![](https://xinhaojin.github.io/imgs-host/past/2020/12/image-9-1024x505.png)

### 二、SSH连接

新建空白txt文件，重命名为ssh，删去后缀，复制文件到SD卡boot根目录

插卡，通电。官方推荐用Putty作为SSH连接工具，默认用户名pi，密码raspberry，注意输入密码时不显示字符

![](https://xinhaojin.github.io/imgs-host/past/2020/12/image-12.png)

![](https://xinhaojin.github.io/imgs-host/past/2020/12/image-13.png)

![](https://xinhaojin.github.io/imgs-host/past/2020/12/image-14.png)

然后输入sudo raspi-config，进入树莓派配置界面

![](https://xinhaojin.github.io/imgs-host/past/2020/12/image-15.png)

选择3 Interface Options，开启SSH和VNC服务

![](https://xinhaojin.github.io/imgs-host/past/2020/12/image-16.png)

![](https://xinhaojin.github.io/imgs-host/past/2020/12/image-17.png)

如果要用VNC操作图形界面，建议再操作一步，确保VNC连接后图形界面可以成功显示（默认设置有时候会提示没法显示）

选择2 Display Options D1 Resolution 修改分辨率

![](https://xinhaojin.github.io/imgs-host/past/2020/12/image-18.png)

### 三、VNC连接

VNC只针对系统镜像带图形界面的。这里使用VNC viewer，地址栏输入IP即可

![](https://xinhaojin.github.io/imgs-host/past/2020/12/image-19.png)

![](https://xinhaojin.github.io/imgs-host/past/2020/12/image-20.png)

![](https://xinhaojin.github.io/imgs-host/past/2020/12/image-21-1024x801.png)