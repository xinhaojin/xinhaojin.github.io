---
title: paddleocr转pytorch推理
index_img: https://xinhaojin.github.io/imgs-host/past/2022/08/example2-1024x724.jpg
tags: []
id: '2425'
categories:
  - - pytorch
date: 2022-08-17 21:48:53
---

[frotms/PaddleOCR2Pytorch: PaddleOCR inference in PyTorch. Converted from [PaddleOCR](https://github.com/PaddlePaddle/PaddleOCR)](https://github.com/frotms/PaddleOCR2Pytorch)

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

[xinhaojin/PaddleOCR2Pytorch: PaddleOCR inference in PyTorch. Converted from [PaddleOCR](https://github.com/PaddlePaddle/PaddleOCR)](https://github.com/xinhaojin/PaddleOCR2Pytorch)

单检测

    python tools/infer/predict_det.py --det_model_path weights/ch_ptocr_v3_det_infer.pth --image_dir doc/imgs/example.jpg

单识别

    python tools/infer/predict_rec.py --rec_model_path weights/ch_ptocr_v3_rec_infer.pth --rec_image_shape 3,48,320 --rec_yaml_path configs/rec/PP-OCRv3/multi_language/chinese_cht_PP-OCRv3_rec.yml --rec_char_dict_path pytorchocr/utils/ppocr_keys_v1.txt  --image_dir doc/imgs_words/ch/word_4.jpg

检测并识别

    python tools/infer/predict_system.py --image_dir doc/imgs/example.jpg --det_model_path weights/ch_ptocr_v3_det_infer.pth --rec_image_shape 3,48,320 --rec_model_path weights/ch_ptocr_v3_rec_infer.pth

![](https://xinhaojin.github.io/imgs-host/past/2022/08/image-1.png)

![](https://xinhaojin.github.io/imgs-host/past/2022/08/example2-1024x724.jpg)