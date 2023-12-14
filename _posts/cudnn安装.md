---
title: cudnn安装
index_img: https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/05/QQ截图20210519140216.png
tags: []
id: '1695'
categories:
  - - 软件安装配置
date: 2021-05-19 14:48:19
---

上一篇已经介绍了cuda的安装方法

于是我尝试运行一个paddle样例，报错，最前面的提示信息是这样的，好的我看懂了

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/05/image-20-1024x129.png)

所以，cudnn是什么？

_cuDNN（CUDA Deep Neural Network library）：是NVIDIA打造的针对深度神经网络的加速库，是一个用于深层神经网络的GPU加速库。 如果你要用GPU训练模型，cuDNN不是必须的，但是一般会采用这个加速库。_

#### 下载安装

[下载地址](https://developer.nvidia.com/rdp/cudnn-download)下载前需要注册登录NVIDIA账号

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/05/QQ截图20210519135720.png)

登录后重新打开下载链接

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/05/QQ截图20210519140216.png)

下载完成后解压，然后把bin、include、lib目录下的文件复制到cuda安装目录下的bin、include、lib目录

这是cuda安装的默认路径C:\\Program Files\\NVIDIA GPU Computing Toolkit\\CUDA\\v10.2

#### 验证安装

在CUDA文件夹中打开cmd

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/05/image-22.png)

输入.\\bandwidthTest.exe和.\\deviceQuery.exe，result=PASS即可

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/05/image-24-1024x493.png)