---
title: yolox训练voc数据集
tags: []
id: '2168'
categories:
  - - 目标检测
date: 2022-03-01 18:57:32
---

## 制作数据集

请参考上一篇使用labelme打标签并制作voc数据集

[labelme标注图片并制作voc数据集](https://xinhaojin.github.io/2022/02/26/labelme%e6%a0%87%e6%b3%a8%e5%9b%be%e7%89%87%e5%b9%b6%e5%88%b6%e4%bd%9cvoc%e6%95%b0%e6%8d%ae%e9%9b%86/)

上文中最后得到了几个文件夹

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/03/image.png)

但这不能直接用，还需要划分训练集和测试集，可以手动划分，或者写段代码随机划分，最后数据集目录结构如下

VOC2007
----Annotations
----JPEGImages
----ImageSets
--------Main
------------test.txt
------------trainval.txt

Annotations是VOC格式的标注文件

JPEGImages是图片文件夹

ImageSets/Main中包含了训练集和测试集的图片名列表，不包含后缀名

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/03/image-1.png)

在VOC2007同级目录执行下面这段代码即可自动划分
```py
import random
import os

train_rate=0.75#训练集比例
xmls=os.listdir('./VOC2007/Annotations')
nums=len(xmls)
train_nums=int(train_rate*nums)
train_index=random.sample(range(nums),train_nums)
print(train_index)

if not os.path.exists('./VOC2007/ImageSets'):
    os.mkdir('./VOC2007/ImageSets')
    os.mkdir('./VOC2007/ImageSets/Main')
trainval=open('./VOC2007/ImageSets/Main/trainval.txt','w')
test=open('./VOC2007/ImageSets/Main/test.txt','w')
for i in range(nums):
    name=xmls[i].split('.')[0]+'\n'
    if i in train_index:
        trainval.write(name)
    else:
        test.write(name)

trainval.close()
test.close()
```
## 安装YOLOX

从github下载即可

[Megvii-BaseDetection/YOLOX: YOLOX is a high-performance anchor-free YOLO, exceeding yolov3~v5 with MegEngine, ONNX, TensorRT, ncnn, and OpenVINO supported. Documentation: https://yolox.readthedocs.io/ (github.com)](https://github.com/Megvii-BaseDetection/YOLOX)

建议新建一个conda环境来安装

参考之前写过的文章

[win10下安装配置yolox](https://xinhaojin.github.io/2021/08/05/win10%e4%b8%8b%e5%ae%89%e8%a3%85%e9%85%8d%e7%bd%aeyolox/)

因为和现在的版本稍有不同，以github项目官方安装指引为准

总结来讲就是在项目根目录成功执行下面两行命令

pip install -r requirement.txt
python setup.py develop

如果安装GPU版本torch，再安装cudnn和cuda即可

## 修改训练相关配置文件

首先把数据集放到datasets目录下，严格按照以下目录结构，会更方便一些

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/03/image-2.png)

### 修改exps/example/yolox_voc/yolox_voc_s.py

num_classes改为标签数目，我这里只有一个标签类别

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/03/image-3.png)

去掉2012相关的内容

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/03/image-4.png)

### 修改tools/train.py

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/03/image-5.png)

\-b 一次训练所抓取的数据样本数量，取决于内存大小，提示memory相关错误优先考虑这个问题，2,4,8,16...

\-d GPU索引，只有一个GPU的话，写0或1是一样的

\-f 试验描述文件路径，也就是上面修改的配置文件

\-c 模型权重文件，使用预训练模型，从官网下载

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/03/image-6.png)

### 修改yolox/data/datasets/voc.py

注释difficult，意思是打标签时对应标签的识别难度，我的标签里没有这一项，不修改会报错，需要把difficult设置为False

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/03/image-7.png)

删去花括号里原有的:s，否则无法正确读取

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/03/image-8.png)

### 修改yolox/data/datasets/voc_classes.pycoco_classes.py(记得改下coco类名，因为后面import类别的时候默认是coco)

注释原有类别，改为自己的，这里只有单类

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/03/image-9.png)

### 修改yolox/evaluators/voc_eval.py

因为没有truncated和difficult标签，所以这里也要修改为0

还有一处是类型转换，添加了float类型转换，否则报错

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/03/image-11.png)

x\["difficult"\]改为0

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/03/image-12.png)

### 修改yolox/exp/yolox_base.py
修改训练轮次，默认300
self.max_epoch = 150

## 训练

python tools/train.py

每10轮会有一次统计，只要下面两个数字不是0就好了，越接近1效果越好

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/03/image-15.png)

## 验证

训练完后的模型权重文件保存在YOLOX_outputs/yolox_voc_s下，最好的权重是best_ckpt.pth

使用这个权重来验证一下

python tools/demo.py image -f exps/example/yolox_voc/yolox_voc_s.py -c YOLOX_outputs/yolox_voc_s/best_ckpt.pth --path datasets/coco/val2017/00001.png --save_result --device gpu

然后在YOLOX_outputs/yolox_voc_s/vis_res下的时间文件夹里就能找到识别结果，如下

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/03/00001.png)

## 另

如果要训练多个数据集，建议看下注意事项：

[yolox训练不同数据集注意事项](https://xinhaojin.github.io/2022/03/31/yolox%e8%ae%ad%e7%bb%83%e4%b8%8d%e5%90%8c%e6%95%b0%e6%8d%ae%e9%9b%86%e6%b3%a8%e6%84%8f%e4%ba%8b%e9%a1%b9/)