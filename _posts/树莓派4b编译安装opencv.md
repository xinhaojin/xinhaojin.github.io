---
title: 树莓派4B编译安装opencv
tags: []
id: '1333'
categories:
  - - 树莓派
date: 2021-01-12 00:45:18
---

pip安装失败，无奈选择手动编译安装

#### 下载opencv-3.4.13

官网https://opencv.org/releases/

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/01/image-35.png)

官网源码是github链接，下载巨慢，github挂代理也不管用，结果试了一下用手机浏览器下载，不到10s就下载完了，现在还有点懵......总之能下载下来就行

这里放一个蓝奏云链接提供下载https://xinhaojin.lanzous.com/iEQV5kamg6j

解压，放到要安装的目录下，进入opencv-3.4.13文件夹

#### 编译安装

分别执行以下命令

    cd  opencv-3.4.13
    mkdir release
    cd release
    cmake -D CMAKE\_BUILD\_TYPE=RELEASE -D CMAKE\_INSTALL\_PREFIX=/usr/local ..
    make
    sudo make install

其中make要等待很长时间，我大概花了一个多小时

结果是\[100%\] Built target opencv verson忘记截图了

    sudo make install
之后是这样的

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/01/image-36.png)

Done.