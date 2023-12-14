---
title: win10下安装配置yolox
tags: []
id: '1871'
categories:
  - - 目标检测
date: 2021-08-05 18:00:24
---

#### 下载源码和模型文件

[https://github.com/Megvii-BaseDetection/YOLOX](https://github.com/Megvii-BaseDetection/YOLOX)

#### 创建一个新环境

conda create -n yolox python==3.7
conda activate yolox

#### 安装依赖

在yolox根目录下执行

pip install -U pip
pip install -r requirements.txt
pip install cython
pip install -U setuptools
python setup.py develop

## cuda配置（针对torch-gpu版本，默认cpu版本可跳过）

cuda以及cudnn的正确安装方式如下两篇文章
https://xinhaojin.github.io/2021/05/18/cuda安装/
https://xinhaojin.github.io/2021/05/19/cudnn安装/

然后按照对应的cuda版本选择安装命令

参考https://pytorch.org/get-started/locally/

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/08/image-4.png)

## 模型文件

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/08/image-5.png)

把下载的模型文件放到yolox/yolox/models下

#### 测试

    python tools/demo.py image -n yolox-s -c yolox/models/yolox_s.pth --path assets/dog.jpg --conf 0.25 --nms 0.45 --tsize 640 --save_result --device [cpu/gpu]

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2021/08/image-7.png)

图片识别成功

#### demo.py主要参数

    --demo  可以是image,video或者wecam,分别用于处理图片、视频和摄像头的视频
    --path  图片或者视频的路径
    --camid  摄像头的序号，一个摄像头的话就是0
    --save_result  不用额外设置参数，添加这一项可以把识别结果保存
    -c  模型文件路径
    --device GPU/CPU计算
    --nms  相似度门限，高于这个值就框出来
    --tsize  图像大小