---
title: ubuntu普通用户安装配置anaconda
tags: []
id: '2102'
categories:
  - - 软件安装配置
date: 2021-12-10 14:48:25
---

#### 下载

[anaconda清华源地址](https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/)

方法一：在服务器上使用wget命令

    wget -c https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-2021.11-Linux-x86\_64.sh

方法二：下载到本地之后通过ftp工具上传到服务器（推荐使用FinalShell）

#### 安装

在下载目录执行下载的bash文件

    bash Anaconda3-2021.11-Linux-x86\_64.sh

根据提示一路yes即可

#### 配置环境变量

安装完之后conda还不能直接使用，需要把conda目录加入到用户变量中

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/12/image.png)

    vi ~/.bashrc 

在最后一行加入

    export PATH=/home/username/anaconda3/bin:$PATH 

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/12/image-1.png)

保存退出，再执行

    source ~/.bashrc

成功

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/12/image-2.png)