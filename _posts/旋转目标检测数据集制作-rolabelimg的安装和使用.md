---
title: 旋转目标检测数据集制作-rolabelimg的安装和使用
tags: []
id: '2298'
categories:
  - - 目标检测
date: 2022-04-05 19:55:27
---

## 安装

下载项目源码

git clone https://github.com/cgvict/roLabelImg
#防止作者跑路，fork了一个备用https://github.com/RuBanNo7/roLabelImg

安装依赖

    pip install pyqt5,lxml

进入到roLabelImg目录下

    pyrcc5 -o resources.py resources.qrc

成功后会在目录下生成rolabelimg.py文件，运行即可启动

    python rolabelimg.py

不要关闭命令行窗口，可以使用软件

## 使用

画矩形框，使用z,x or c,v进行旋转调节，其余用法和普通labelImg相同

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/04/20191211101740462.png)

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/04/image-2.png)