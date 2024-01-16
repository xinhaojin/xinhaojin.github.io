---
title: yolov5的环境配置
tags: []
id: '1481'
categories:
  - - 目标检测
date: 2021-04-12 21:28:16
---

![](https://xinhaojin.github.io/imgs-host/past/2021/04/image-7.png)

#### 下载源码

官方地址[https://github.com/ultralytics/yolov5](https://github.com/ultralytics/yolov5)

#### 安装依赖

下载完成后解压，进入requirement.txt所在目录，执行

pip install -r requirements.txt

或者，你可以直接查看requirement.txt内的版本要求，实际测试中发现略低于这些版本也可使用，以下版本已经是我略降低后的标准

\# pip install -r requirements.txt

    # base ----------------------------------------
    matplotlib>=3.2.2
    numpy>=1.18.5
    opencv-python>=4.1.2
    Pillow
    PyYAML>=5.3.1
    scipy>=1.4.1
    torch>=1.7.0
    torchvision>=0.8.1
    tqdm>=4.41.0

    # logging -------------------------------------
    tensorboard>=2.4.1
    # wandb

    # plotting ------------------------------------
    seaborn>=0.11.0
    pandas

    # export --------------------------------------
    # coremltools>=4.1
    # onnx>=1.8.1
    # scikit-learn==0.19.2  # for coreml quantization

    # extras --------------------------------------
    thop  # FLOPS computation
    pycocotools>=2.0  # COCO mAP

安装步骤看起来简单，装起来是真的不容易，由于网络问题，最好先将pip换源，我自己使用清华源时经常出错，阿里云镜像源好像更加稳定一些，如安装xxx依赖包，可使用以下命令从阿里云镜像源加速下载

    pip install xxx -i http://mirrors.aliyun.com/pypi/simple

在win10上安装还好说一点，我的最终应用平台是树莓派，装起来更加艰难，印象中比较难安装的是opencv、torch、matplotlib，如果在线安装失败，建议直接选择下载包到本地手动安装，其中最难的是torch1.7以上版本的安装，我发现在Debian10系统下，pip install torch只支持到torch0.4的版本，在官网又找不到树莓派支持的版本，下载了好几个都说平台不支持，最后在网上找到了树莓派可用的版本**[pytorch-armv7l](https://gitee.com/jin_xin_hao/pytorch-armv7l)**，写本文的时候突然上不去GitHub，此链接是克隆在码云上的项目

#### 验证

这么复杂的环境，装完总要验证一下才放心。运行detect.py，source参数改为0，即调用摄像头

    python detect.py --source=0 --weights="weights/yolov5s.pt"

其中pt文件是权重文件，源码中没有自带，需要自行下载

在weights文件夹中，有一个download_weights.sh文件，内容如下，写明了权重文件的下载方式，默认是会自动下载，但往往也因为网络问题很难一次下载成功

    #!/bin/bash
    # Download latest models from https://github.com/ultralytics/yolov5/releases
    # Usage:
    #    $ bash weights/download_weights.sh

    python - <<EOF
    from utils.google_utils import attempt_download

    for x in ['s', 'm', 'l', 'x']:
        attempt_download(f'yolov5{x}.pt')

    EOF

这里也放上[蓝奏云链接](https://xinhaojin.lanzous.com/iHb5xnza7mh)，不过只有一个yolov5s.pt，这是官方给的最小最快的模型，下载下来放在weights文件夹下即可，如果一切顺利，则会调取摄像头识别物体。

#### Done.