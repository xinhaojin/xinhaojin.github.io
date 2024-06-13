---
title: yolov5训练自己的数据集
tags: []
id: '1487'
categories:
  - - 目标检测
date: 2021-04-12 22:51:39
---

#### 前期准备

[yolov5的环境配置](https://xinhaojin.github.io/2021/04/12/yolov5的环境配置/)

[制作yolov5数据集](https://xinhaojin.github.io/2021/04/12/目标检测-制作数据集/)

#### 配置文件修改

1.复制data目录下的coco.yaml，重命名为自定义的，如test.yaml

要修改的内容是，图片路径，类别数，类别名称

![](https://xinhaojin.github.io/imgs-host/past/2021/04/image-9-1024x416.png)

![](https://xinhaojin.github.io/imgs-host/past/2021/04/image-10.png)

可以看到我修改后的图片路径的表达方式不同，是直接给出图片的目录，建议就像我这样改，目录结构在之前文章中介绍过

![](https://xinhaojin.github.io/imgs-host/past/2021/04/image-11.png)

2.修改weights目录下的yolov5s.yaml（要用哪个就修改哪个）

修改内容是类别数目

![](https://xinhaojin.github.io/imgs-host/past/2021/04/image-12.png)

3.修改根目录下的train.py

修改内容为几个重要参数，如上配置的话，就应该这样修改

![](https://xinhaojin.github.io/imgs-host/past/2021/04/image-14-1024x291.png)

其中epochs是迭代次数，默认300，时间太久了，先改小一点试试，batch-size貌似跟显卡配置和内存有关，建议也先改小一点试试

执行train.py就可以开始训练了，结果如下

![](https://xinhaojin.github.io/imgs-host/past/2021/04/image-15-1024x616.png)

![](https://xinhaojin.github.io/imgs-host/past/2021/04/image-17-1024x420.png)

![](https://xinhaojin.github.io/imgs-host/past/2021/04/image-18-1024x775.png)

#### 测试

编辑根目录下的test.py

修改权重文件为以上训练出的结果（上图标红），其他参数修改如下

![](https://xinhaojin.github.io/imgs-host/past/2021/04/image-19-1024x353.png)

执行test.py后就会去识别测试集中的图片

![](https://xinhaojin.github.io/imgs-host/past/2021/04/image-21-1024x99.png)

像我这样的一个简单目标是很容易检测出来的

![](https://xinhaojin.github.io/imgs-host/past/2021/04/test_batch0_pred-1024x474.jpg)

#### 正向推理

编辑detect.py，同样地，修改主要参数，训练出的权重文件和想要测试的图片文件夹路径

![](https://xinhaojin.github.io/imgs-host/past/2021/04/image-22-1024x197.png)

结果如下

![](https://xinhaojin.github.io/imgs-host/past/2021/04/image-23.png)