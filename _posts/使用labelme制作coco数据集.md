---
title: 使用labelme制作coco数据集
tags: []
id: '2013'
categories:
  - - 目标检测
date: 2021-09-02 15:07:55
---

#### 安装labelme

直接使用pip安装

    pip install labelme

#### 运行

命令行执行

    labelme

打开后选择打开文件夹，选择自动保存

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/09/image-1.png)

编辑，画矩形，然后打上标签

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/09/image-2-1024x626.png)

在图片目录下会保存json格式的标签信息

#### 应用（以yolox为例）

yolox的数据集readme中写明了需要的数据集格式

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/09/image-3.png)

其中instancesxxx.json是很多json的集合，需要手动生成

现在要做的是，先手动划分训练集和测试集，再生成对应标签的json集合

下载labelme2coco.py

[https://github.com/Tony607/labelme2coco](https://github.com/Tony607/labelme2coco)

进入json文件夹，执行

    python labelme2coco.py ./

会生成一个大json文件，改成需要的文件名使用即可