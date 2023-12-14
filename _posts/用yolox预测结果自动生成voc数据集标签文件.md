---
title: 用yolox预测结果自动生成voc数据集标签文件
tags: []
id: '2413'
categories:
  - - 目标检测
date: 2022-07-12 15:59:00
---

## 问题

已有少量（100张）数据集由手工标注，训练结果也不算差，但希望能够进一步提高准确率，需要扩充数据集样本，手工标注太累，因此利用已有训练权重进行预测，用预测结果自动生成xml格式的标注文件，当然也会存在预测错误的情况，自定义条件筛选正确，其余手工标注即可。

## voc数据集的xml文件格式
```xml
<annotation>
<folder>jiyan[_new</folder>
<filename>00002.png</filename>
<path>E:[[Y1[[验证码[[数据集[[jiyan[_new[[00002.png</path>
<source>
<database>Unknown</database>
</source>
<size>
<width>300</width>
<height>200</height>
<depth>3</depth>
</size>
<segmented>0</segmented>
<object>
<name>word</name>
<pose>Unspecified</pose>
<truncated>0</truncated>
<difficult>0</difficult>
<bndbox>
<xmin>123</xmin>
<ymin>52</ymin>
<xmax>177</xmax>
<ymax>106</ymax>
</bndbox>
</object>
<object>
<name>word</name>
<pose>Unspecified</pose>
<truncated>0</truncated>
<difficult>0</difficult>
<bndbox>
<xmin>206</xmin>
<ymin>16</ymin>
<xmax>260</xmax>
<ymax>70</ymax>
</bndbox>
</object>
<object>
<name>word</name>
<pose>Unspecified</pose>
<truncated>0</truncated>
<difficult>0</difficult>
<bndbox>
<xmin>194</xmin>
<ymin>90</ymin>
<xmax>247</xmax>
<ymax>142</ymax>
</bndbox>
</object>
</annotation>
```
## 修改预测代码

使得针对每场图片，输出预测矩形框及对应的类别

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/07/image.png)

## 生成xml文件

拿到矩形框和类别名，就可以写xml文件了，按照上面的格式写就行，有些标签是次要的，并不会用到，可以省略。

这个函数是copilot插件自动生成的，我只写了个函数名，连参数都没写，就给我自动补全了，你敢信？属实牛逼！

我只改动了两三行，再加个import，就能用了
```py
import xml.etree.ElementTree as et
def box2xml(bboxes, class[_names, save[_path):
    root = et.Element('annotation')
    et.SubElement(root, 'folder').text = 'VOC2007'
    #et.SubElement(root, 'filename').text = os.path.basename(save[_path)
    et.SubElement(root, 'source').text = ' '.join([['VOC2007', 'createed by YOLOX'[])
    #et.SubElement(root, 'segmented').text = '0'
    size = et.SubElement(root, 'size')
    et.SubElement(size, 'width').text = '300'
    et.SubElement(size, 'height').text = '200'
    et.SubElement(size, 'depth').text = '3'
    for i in range(bboxes.shape[[0[]):
        obj = et.SubElement(root, 'object')
        et.SubElement(obj, 'name').text = class[_names[[i[]
        et.SubElement(obj, 'pose').text = 'Unspecified'
        et.SubElement(obj, 'truncated').text = '0'
        et.SubElement(obj, 'difficult').text = '0'
        bbox = et.SubElement(obj, 'bndbox')
        et.SubElement(bbox, 'xmin').text = str(bboxes[[i, 0[])
        et.SubElement(bbox, 'ymin').text = str(bboxes[[i, 1[])
        et.SubElement(bbox, 'xmax').text = str(bboxes[[i, 2[])
        et.SubElement(bbox, 'ymax').text = str(bboxes[[i, 3[])
    tree = et.ElementTree(root)
    tree.write(save[_path)
    print('save xml file to {}'.format(save[_path))
```
## 怎么确定标注的正确性？

具体问题具体分析

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/07/00001.jpg)

针对我的场景，正确标注应该是3个框，已有权重有时会只检测出两个框，因此我的筛选条件就是，预测框个数<3的，把文件名汇总起来最后输出，我自己再修改即可。

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/07/image-1-1024x108.png)

这样工作量就从标注500多张图片锐减到30多张。

## 代码

由tools/demo.py修改
```py
#!/usr/bin/env python3
# -[*- coding:utf-8 -[*-
# Copyright (c) Megvii, Inc. and its affiliates.

import argparse
import os
import time
from loguru import logger
import cv2
import numpy as np
import torch

from yolox.data.data[_augment import ValTransform
from yolox.data.datasets import COCO[_CLASSES
from yolox.exp import get[_exp
from yolox.utils import fuse[_model, get[_model[_info, postprocess, vis

import xml.etree.ElementTree as et

IMAGE[_EXT = [[".jpg", ".jpeg", ".webp", ".bmp", ".png"[]
#python tools/predict2xml.py image -n yolox-s -c weights/90jiyan.pth --path /home/jxh/work/YOLOX/datasets/VOCdevkit/VOC2007/JPEGImages --conf 0.6 --nms 0.6 --save[_result --device [[cpu/gpu[]
def make[_parser():
    parser = argparse.ArgumentParser("YOLOX Demo!")
    parser.add[_argument(
        "demo", default="image", help="demo type, eg. image, video and webcam"
    )
    parser.add[_argument("-expn", "--experiment-name", type=str, default='test')
    parser.add[_argument("-n", "--name", type=str, default='yolox-s', help="model name")

    parser.add[_argument(
        "--path", default="./assets/test", help="path to images or video"
    )
    parser.add[_argument("--camid", type=int, default=0, help="webcam demo camera id")
    parser.add[_argument(
        "--save[_result",
        action="store[_true",
        help="whether to save the inference result of image/video",
    )

    # exp file
    parser.add[_argument(
        "-f",
        "--exp[_file",
        default='exps/example/yolox[_voc/yolox[_voc[_s.py',
        type=str,
        help="pls input your experiment description file",
    )
    parser.add[_argument("-c", "--ckpt", default='YOLOX[_outputs/yolox[_voc[_s/90jiyan[_new.pth', type=str, help="ckpt for eval")
    parser.add[_argument(
        "--device",
        default="cpu",
        type=str,
        help="device to run our model, can either be cpu or gpu",
    )
    parser.add[_argument("--conf", default=0.5, type=float, help="test conf")
    parser.add[_argument("--nms", default=0.5, type=float, help="test nms threshold")
    parser.add[_argument("--tsize", default=None, type=int, help="test img size")
    parser.add[_argument(
        "--fp16",
        dest="fp16",
        default=False,
        action="store[_true",
        help="Adopting mix precision evaluating.",
    )
    parser.add[_argument(
        "--legacy",
        dest="legacy",
        default=False,
        action="store[_true",
        help="To be compatible with older versions",
    )
    parser.add[_argument(
        "--fuse",
        dest="fuse",
        default=False,
        action="store[_true",
        help="Fuse conv and bn for testing.",
    )
    parser.add[_argument(
        "--trt",
        dest="trt",
        default=False,
        action="store[_true",
        help="Using TensorRT model for testing.",
    )
    return parser


def get[_image[_list(path):
    image[_names = [[[]
    for maindir, subdir, file[_name[_list in os.walk(path):
        for filename in file[_name[_list:
            apath = os.path.join(maindir, filename)
            ext = os.path.splitext(apath)[[1[]
            if ext in IMAGE[_EXT:
                image[_names.append(apath)
    return image[_names

def box2xml(bboxes, class[_names, save[_path):
    root = et.Element('annotation')
    et.SubElement(root, 'folder').text = 'VOC2007'
    #et.SubElement(root, 'filename').text = os.path.basename(save[_path)
    et.SubElement(root, 'source').text = ' '.join([['VOC2007', 'createed by YOLOX'[])
    #et.SubElement(root, 'segmented').text = '0'
    size = et.SubElement(root, 'size')
    et.SubElement(size, 'width').text = '300'
    et.SubElement(size, 'height').text = '200'
    et.SubElement(size, 'depth').text = '3'
    for i in range(bboxes.shape[[0[]):
        obj = et.SubElement(root, 'object')
        et.SubElement(obj, 'name').text = class[_names[[i[]
        et.SubElement(obj, 'pose').text = 'Unspecified'
        et.SubElement(obj, 'truncated').text = '0'
        et.SubElement(obj, 'difficult').text = '0'
        bbox = et.SubElement(obj, 'bndbox')
        et.SubElement(bbox, 'xmin').text = str(bboxes[[i, 0[])
        et.SubElement(bbox, 'ymin').text = str(bboxes[[i, 1[])
        et.SubElement(bbox, 'xmax').text = str(bboxes[[i, 2[])
        et.SubElement(bbox, 'ymax').text = str(bboxes[[i, 3[])
    tree = et.ElementTree(root)
    tree.write(save[_path)
    print('save xml file to {}'.format(save[_path))

class Predictor(object):
    def [_[_init[_[_(
        self,
        model,
        exp,
        cls[_names=COCO[_CLASSES,
        trt[_file=None,
        decoder=None,
        device="cpu",
        fp16=False,
        legacy=False,
    ):
        self.model = model
        self.cls[_names = cls[_names
        self.decoder = decoder
        self.num[_classes = exp.num[_classes
        self.confthre = exp.test[_conf
        self.nmsthre = exp.nmsthre
        self.test[_size = exp.test[_size
        self.device = device
        self.fp16 = fp16
        self.preproc = ValTransform(legacy=legacy)
        if trt[_file is not None:
            from torch2trt import TRTModule

            model[_trt = TRTModule()
            model[_trt.load[_state[_dict(torch.load(trt[_file))

            x = torch.ones(1, 3, exp.test[_size[[0[], exp.test[_size[[1[]).cuda()
            self.model(x)
            self.model = model[_trt

    def inference(self, img):
        img[_info = {"id": 0}
        if isinstance(img, str):
            img[_info[["file[_name"[] = os.path.basename(img)
            img = cv2.imread(img)
        else:
            img[_info[["file[_name"[] = None

        height, width = img.shape[[:2[]
        img[_info[["height"[] = height
        img[_info[["width"[] = width
        img[_info[["raw[_img"[] = img

        ratio = min(self.test[_size[[0[] / img.shape[[0[], self.test[_size[[1[] / img.shape[[1[])
        img[_info[["ratio"[] = ratio

        img, [_ = self.preproc(img, None, self.test[_size)
        img = torch.from[_numpy(img).unsqueeze(0)
        img = img.float()
        if self.device == "gpu":
            img = img.cuda()
            if self.fp16:
                img = img.half()  # to FP16

        with torch.no[_grad():
            t0 = time.time()
            outputs = self.model(img)
            if self.decoder is not None:
                outputs = self.decoder(outputs, dtype=outputs.type())
            outputs = postprocess(
                outputs, self.num[_classes, self.confthre,
                self.nmsthre, class[_agnostic=True
            )
            logger.info("Infer time: {:.4f}s".format(time.time() - t0))
        return outputs, img[_info

    def visual(self, output, img[_info, cls[_conf=0.5):
        #print(img[_info)
        '''
        {'id': 0, 'file[_name': '00032团圆汤.png', 'height': 200, 'width': 300,'''
        ratio = img[_info[["ratio"[]
        img = img[_info[["raw[_img"[]
        if output is None:
            return img
        output = output.cpu()

        bboxes = output[[:, 0:4[]
        # preprocessing: resize
        bboxes /= ratio
        boxes=bboxes.numpy()
        boxes = boxes.astype(int)
        print(boxes)
        cls = output[[:, 6[]
        clses= cls.numpy()
        #转为list
        clses = clses.tolist()
        classnames = [[self.cls[_names[[int(i)[] for i in clses[]
        print(classnames)
        box2xml(boxes, classnames, 'xml[_output/'+img[_info[['file[_name'[].split('.')[[0[]+'.xml')
        scores = output[[:, 4[] [* output[[:, 5[]

        vis[_res = vis(img, bboxes, scores, cls, cls[_conf, self.cls[_names)
        return vis[_res,bboxes

def image[_demo(predictor, vis[_folder, path, current[_time, save[_result):
    if os.path.isdir(path):
        files = get[_image[_list(path)
    else:
        files = [[path[]
    files.sort()
    error=[[[]
    for image[_name in files:
        outputs, img[_info = predictor.inference(image[_name)
        result[_image,bboxes = predictor.visual(outputs[[0[], img[_info, predictor.confthre)
        if len(bboxes)<3:
            error.append(image[_name)
        if save[_result:
            save[_folder = os.path.join(
                vis[_folder, time.strftime("%Y[_%m[_%d[_%H[_%M[_%S", current[_time)
            )
            os.makedirs(save[_folder, exist[_ok=True)
            save[_file[_name = os.path.join(save[_folder, os.path.basename(image[_name))
            logger.info("Saving detection result in {}".format(save[_file[_name))
            ####################################
            bboxes=bboxes.numpy()
            bboxes = bboxes.astype(int)
            log[_file = open('log.txt','a+')
            log[_file.write(save[_file[_name+"[[n")
            log[_file.write(str(bboxes)+"[[n")
            log[_file.close()
            ###################################
            # result[_image=koutu(result[_image,bboxes)
            cv2.imwrite(save[_file[_name, result[_image)
        ch = cv2.waitKey(0)
        if ch == 27 or ch == ord("q") or ch == ord("Q"):
            break
    print('错误标注文件:',error)


def imageflow[_demo(predictor, vis[_folder, current[_time, args):
    cap = cv2.VideoCapture(args.path if args.demo == "video" else args.camid)
    width = cap.get(cv2.CAP[_PROP[_FRAME[_WIDTH)  # float
    height = cap.get(cv2.CAP[_PROP[_FRAME[_HEIGHT)  # float
    fps = cap.get(cv2.CAP[_PROP[_FPS)
    if args.save[_result:
        save[_folder = os.path.join(
            vis[_folder, time.strftime("%Y[_%m[_%d[_%H[_%M[_%S", current[_time)
        )
        os.makedirs(save[_folder, exist[_ok=True)
        if args.demo == "video":
            save[_path = os.path.join(save[_folder, os.path.basename(args.path))
        else:
            save[_path = os.path.join(save[_folder, "camera.mp4")
        logger.info(f"video save[_path is {save[_path}")
        vid[_writer = cv2.VideoWriter(
            save[_path, cv2.VideoWriter[_fourcc([*"mp4v"), fps, (int(width), int(height))
        )
    while True:
        ret[_val, frame = cap.read()
        if ret[_val:
            outputs, img[_info = predictor.inference(frame)
            result[_frame = predictor.visual(outputs[[0[], img[_info, predictor.confthre)
            if args.save[_result:
                vid[_writer.write(result[_frame)
            else:
                cv2.namedWindow("yolox", cv2.WINDOW[_NORMAL)
                cv2.imshow("yolox", result[_frame)
            ch = cv2.waitKey(1)
            if ch == 27 or ch == ord("q") or ch == ord("Q"):
                break
        else:
            break


def main(exp, args):
    if not args.experiment[_name:
        args.experiment[_name = exp.exp[_name

    file[_name = os.path.join(exp.output[_dir, args.experiment[_name)
    os.makedirs(file[_name, exist[_ok=True)

    vis[_folder = None
    if args.save[_result:
        vis[_folder = os.path.join(file[_name, "vis[_res")
        os.makedirs(vis[_folder, exist[_ok=True)

    if args.trt:
        args.device = "gpu"

    logger.info("Args: {}".format(args))

    if args.conf is not None:
        exp.test[_conf = args.conf
    if args.nms is not None:
        exp.nmsthre = args.nms
    if args.tsize is not None:
        exp.test[_size = (args.tsize, args.tsize)

    model = exp.get[_model()
    logger.info("Model Summary: {}".format(get[_model[_info(model, exp.test[_size)))

    if args.device == "gpu":
        model.cuda()
        if args.fp16:
            model.half()  # to FP16
    model.eval()

    if not args.trt:
        if args.ckpt is None:
            ckpt[_file = os.path.join(file[_name, "best[_ckpt.pth")
        else:
            ckpt[_file = args.ckpt
        logger.info("loading checkpoint")
        ckpt = torch.load(ckpt[_file, map[_location="cpu")
        # load the model state dict
        model.load[_state[_dict(ckpt[["model"[])
        logger.info("loaded checkpoint done.")

    if args.fuse:
        logger.info("[[tFusing model...")
        model = fuse[_model(model)

    if args.trt:
        assert not args.fuse, "TensorRT model is not support model fusing!"
        trt[_file = os.path.join(file[_name, "model[_trt.pth")
        assert os.path.exists(
            trt[_file
        ), "TensorRT model is not found![[n Run python3 tools/trt.py first!"
        model.head.decode[_in[_inference = False
        decoder = model.head.decode[_outputs
        logger.info("Using TensorRT to inference")
    else:
        trt[_file = None
        decoder = None

    predictor = Predictor(
        model, exp, COCO[_CLASSES, trt[_file, decoder,
        args.device, args.fp16, args.legacy,
    )
    current[_time = time.localtime()
    if args.demo == "image":
        image[_demo(predictor, vis[_folder, args.path, current[_time, args.save[_result)
    elif args.demo == "video" or args.demo == "webcam":
        imageflow[_demo(predictor, vis[_folder, current[_time, args)


if [_[_name[_[_ == "[_[_main[_[_":
    args = make[_parser().parse[_args()
    exp = get[_exp(args.exp[_file, args.name)

    main(exp, args)
```