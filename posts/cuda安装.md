---
title: cuda安装
index_img: https://xinhaojin.github.io/imgs-host/past/2021/05/image-13-1024x490.png
tags: []
id: '1678'
categories:
  - - 软件安装配置
date: 2021-05-18 23:53:28
---

#### 确定安装版本

首先查看显卡信息，一般都是用的英伟达显卡，打开NVIDIA控制面板-帮助-系统信息-显示，查看驱动程序版本

![](https://xinhaojin.github.io/imgs-host/past/2021/05/image-9.png)

使用驱动精灵查看可更新的驱动版本（只用于查看，不需要在这更新驱动，cuda安装时会自动更新驱动）

![](https://xinhaojin.github.io/imgs-host/past/2021/05/image-10-1024x664.png)

最后对照这张[表](https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html)，查看可以安装的cuda版本，对于我的机器而言，更新驱动后最高能安装cuda10.2版本

![](https://xinhaojin.github.io/imgs-host/past/2021/05/image-12-1024x504.png)

#### 下载

在[这里](https://developer.nvidia.com/cuda-toolkit-archive)选择我要安装的版本

![](https://xinhaojin.github.io/imgs-host/past/2021/05/image-14.png)

点击下载链接

![](https://xinhaojin.github.io/imgs-host/past/2021/05/image-13-1024x490.png)

#### 安装

打开安装包，默认路径即可，保证C盘空间充足

![](https://xinhaojin.github.io/imgs-host/past/2021/05/image-15.png)

![](https://xinhaojin.github.io/imgs-host/past/2021/05/image-16.png)

安装完成后再次打开NVIDIA控制面板-帮助-系统信息

驱动成功更新

![](https://xinhaojin.github.io/imgs-host/past/2021/05/image-17.png)

cuda10.2也安装成功

![](https://xinhaojin.github.io/imgs-host/past/2021/05/image-18.png)

cmd输入nvcc -V能看到版本号

![](https://xinhaojin.github.io/imgs-host/past/2021/05/image-19.png)

Done.