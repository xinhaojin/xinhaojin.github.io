---
title: windows安装wget+用法
tags: []
id: '1723'
categories:
  - - 软件安装配置
date: 2021-05-21 15:13:56
---

wget是一个轻量级的可配置下载工具

#### windows下安装wget

下载地址->[这里](https://jaist.dl.sourceforge.net/project/gnuwin32/wget/1.11.4-1/)

安装时记住安装目录，这玩意儿压根不占空间，随他默认即可

在高级系统设置-环境变量-系统变量中添加一个字段

    GNU_HOME=C:\\Program Files (x86)\\GnuWin32

在系统变量Path中添加;%GNU_HOME%\\bin（win7注意前面有分号）

![](https://xinhaojin.github.io/imgs-host/past/2021/05/image-30.png)

#### 用法

**直接下载单个文件** wget url

    wget https://down.qq.com/qqweb/PCQQ/PCQQ_EXE/PCQQ2021.exe

**自定义文件名** wget -O name url

wget默认会以最后一个符合”/”的后面的字符来命令，对于动态链接的下载通常文件名会不正确。 

下面的例子会下载一个文件并以名称download.php?id=1080保存 

    wget http://www.xxxx.xxx/download?id=1080

即使下载的文件是zip格式，它仍然以download.php?id=1080命名

为了解决这个问题，我们可以使用参数-O来指定一个文件名

    wget -O abc.zip http://www.xxxx.xxx/download.php?id=1080 

**断点续传** wget -c

对于大文件下载时间较长，过程中可能会出现网络中断，这时使用wget -c url便可以续传

**后台下载** wget -b

    wget -b url

查看进度可以使用wget-log