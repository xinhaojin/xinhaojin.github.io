---
title: Yolox+Siamese孪生神经网络:图标点选验证码识别
tags: []
id: '2435'
categories:
  - - 目标检测
date: 2022-09-15 21:42:26
---

## 问题描述

[图标点选验证码_点选验证码_验证码API_在线体验_网易易盾 (163.com)](https://dun.163.com/trial/icon-click)

网页场景

![](https://xinhaojin.github.io/imgs-host/past/2022/09/image.png)

图片下载下来是这样的，底部会有几行小图标，第三行小图标是正确的顺序

这个问题的目标就是按顺序给出三个坐标点。

![](https://xinhaojin.github.io/imgs-host/past/2022/09/00001.jpg)

## 解决方案

### step1.使用yolox检测出所有图标的位置（不包含下面的三行图标）

因为图标种类众多，没法直接用yolox进行图标详细分类，所以我训练的时候只分了两类，一类白的，一类黑的，虽然在本文写的方法中这个类别并没有发挥作用，但考虑到标注一类和两类的工作量是一样的，所以还是标了两类，而且如果采用其他方法对图标进行预处理，这个分类还是有用的。

yolox部分不做介绍了，之前也写过几篇相关的文章，可以参考。

[“yolox”的搜索结果 – Xinhao Jin](https://blog.xinhaojin.top/?s=yolox)

[Megvii-BaseDetection/YOLOX: YOLOX is a high-performance anchor-free YOLO, exceeding yolov3~v5 with MegEngine, ONNX, TensorRT, ncnn, and OpenVINO supported. Documentation: https://yolox.readthedocs.io/ (github.com)](https://github.com/Megvii-BaseDetection/YOLOX)

效果如下图

![](https://xinhaojin.github.io/imgs-host/past/2022/09/image-1.png)

### step2.使用孪生神经网络训练图像相似度

借用了大佬提供的代码，特征提取网络采用vgg16

[bubbliiiing/Siamese-pytorch: 这是一个孪生神经网络（Siamese network）的库，可进行图片的相似性比较。 (github.com)](https://github.com/bubbliiiing/Siamese-pytorch)

需要关注的是数据集的格式，就是一个类别一个文件夹，里面放同一类别的图片，相似度记为1，不同文件夹里的图片是不同类的，相似度记为0。

**快速打标签制作数据集的方法**

yolox框出来的5张小图，从左到右记为1-5，给出图标正确顺序对应的记号，如，例图的标签为243，

![](https://xinhaojin.github.io/imgs-host/past/2022/09/00001-1.jpg)

打开图片文件夹放大，打开一个记事本即可快速打标签

![](https://xinhaojin.github.io/imgs-host/past/2022/09/image-2.png)

打完标签，结合yolox给出的位置框，即可扣出图片，每张验证码图片可以形成3对图片（3个类别），每个类别有3张图：验证码图片下方给出的黑图、白图，以及嵌入背景的图标。

![](https://xinhaojin.github.io/imgs-host/past/2022/09/image-3.png)

![](https://xinhaojin.github.io/imgs-host/past/2022/09/image-4.png)

### 数据集去重

开头提到图标种类很多，但也会有重复的，因此如果仅按照上面的流程，会导致同一类图标放进了两个不同文件夹，神经网络就不懂了，到底是要算一类还是两类？

所以需要给数据集去重，确保同一类的放在同一个文件夹，方法是取出每个文件夹的第一个图标（1.jpg是验证码图片下方三排中的黑底白图），进行相似度计算，如果足够相似，则把两个文件夹合并。

怎么判断足够相似？如果同一个图标（黑底白图）出现在不同验证码图片中，那么他们几乎是像素级一致的，可以进行二值化后将两张图片相减，取差值绝对值之和，如果这个值足够小，说明是同一类的图标。

### 训练&预测

训练过程省略

预测就是输入两个图片，给出相似度

### 结果处理

预测得到一个3*5的相似度矩阵，循环选取相似度最大值并删除所在行列即可得到最佳匹配结果，结果绘图如下

![](https://xinhaojin.github.io/imgs-host/past/2022/09/image-5.png)

Done.