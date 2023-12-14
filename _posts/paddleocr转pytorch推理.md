---
title: paddleocr转pytorch推理
index_img: https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/08/example2-1024x724.jpg
tags: []
id: '2425'
categories:
  - - pytorch
date: 2022-08-17 21:48:53
---

[frotms/PaddleOCR2Pytorch: PaddleOCR inference in PyTorch. Converted from \[PaddleOCR\](https://github.com/PaddlePaddle/PaddleOCR)](https://github.com/frotms/PaddleOCR2Pytorch)

## 安装

    paddlepaddle==2.0.0
    shapely
    numpy
    pillow
    pyclipper
    opencv-python <= 4.2.0.32
    pytorch
    pyyaml
    scikit-image

## 中英文推理

fork了一个，把重要的两个检测识别模型直接加到了主分支中

[xinhaojin/PaddleOCR2Pytorch: PaddleOCR inference in PyTorch. Converted from \[PaddleOCR\](https://github.com/PaddlePaddle/PaddleOCR)](https://github.com/xinhaojin/PaddleOCR2Pytorch)

单检测

    python tools/infer/predict\_det.py --det\_model\_path weights/ch\_ptocr\_v3\_det\_infer.pth --image\_dir doc/imgs/example.jpg

单识别

    python tools/infer/predict\_rec.py --rec\_model\_path weights/ch\_ptocr\_v3\_rec\_infer.pth --rec\_image\_shape 3,48,320 --rec\_yaml\_path configs/rec/PP-OCRv3/multi\_language/chinese\_cht\_PP-OCRv3\_rec.yml --rec\_char\_dict\_path pytorchocr/utils/ppocr\_keys\_v1.txt  --image\_dir doc/imgs\_words/ch/word\_4.jpg

检测并识别

    python tools/infer/predict\_system.py --image\_dir doc/imgs/example.jpg --det\_model\_path weights/ch\_ptocr\_v3\_det\_infer.pth --rec\_image\_shape 3,48,320 --rec\_model\_path weights/ch\_ptocr\_v3\_rec\_infer.pth

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/08/image-1.png)

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/08/example2-1024x724.jpg)