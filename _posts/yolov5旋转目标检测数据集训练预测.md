---
title: yolov5旋转目标检测(数据集,训练,预测)
tags: []
id: '2306'
categories:
  - - 目标检测
date: 2022-04-07 16:54:24
---

## 前言

目标检测模型一般都是用无角度的矩形框标注的，但有些时候我们的目标在图像中是有角度的，我们希望框出来的目标矩形框也是有角度的贴合目标的，比如下面这种情况

（虽然训练结果不好，可能是数据集数量少，或者对这种类型的数据集效果不理想之类的，但还是记录一下过程）

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/04/image-4.png)

yolov5本身是一个轻量级的目标检测模型。不支持旋转目标矩形框。

此次用到的是牛人改写的yolov5-obb

[hukaixuan19970627/yolov5_obb: yolov5 + csl_label.(Oriented Object Detection)（Rotation Detection）（Rotated BBox）基于yolov5的旋转目标检测 (github.com)](https://github.com/hukaixuan19970627/yolov5_obb)

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/04/image-5.png)

## 项目环境准备

git clone https://github.com/hukaixuan19970627/yolov5_obb.git
cd yolov5_obb
pip install -r requirements.txt
cd utils/nms_rotated
python setup.py develop  #or "pip install -v -e ."

其中最后一步可能会出错，属于CUDA环境问题，如果torch.cuda.is_available()==True的话，那应该是cuda toolkit的问题，CUDA_HOME环境变量之类的问题

## 数据集格式

看一下dataset/datasetdemo下的示例

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/04/image-6.png)

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/04/image-7.png)

结合介绍看一下

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/04/image-8-1024x569.png)

可以看出，图片标签的格式是倾斜矩形框的四个点，类别名，是否difficult

## 用rolabelimg给图片打标签

[旋转目标检测数据集制作-rolabelimg的安装和使用 – Xinhao Jin](https://xinhaojin.github.io/2022/04/05/%e6%97%8b%e8%bd%ac%e7%9b%ae%e6%a0%87%e6%a3%80%e6%b5%8b%e6%95%b0%e6%8d%ae%e9%9b%86%e5%88%b6%e4%bd%9c-rolabelimg%e7%9a%84%e5%ae%89%e8%a3%85%e5%92%8c%e4%bd%bf%e7%94%a8/)

标注完之后得到的是xml格式的标注文件

需要转换为上述格式的txt文件（4个点1个类别1个difficult）

找了一下好像项目中没有用于数据集转换的代码，可能作者觉得这很简单。。。

## 转换数据集格式

想想应该也不是很难，自己动手，丰衣足食，首先把xml中的robndbox标签内容读出来，这时候才发现，怎么不是四个点的坐标？

而是中心点坐标，髋，高，旋转角度？？

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/04/image-9.png)

坐标转换函数如下
```py
def rotate(cx, cy, w, h, angle):
    # 用于常规坐标时，angle是顺时针旋转角度
    # 用于图像坐标时，angle是逆时针旋转角度
    # math.cos(angle)这里的angle单位是rad
    # angle = angle/180*math.pi  单位转换
    angle=-angle#这里是图像坐标
    points = [[cx-w/2, cy-h/2], [cx+w/2, cy-h/2],
              [cx+w/2, cy+h/2], [cx-w/2, cy+h/2]]
    newpoints = []
    if angle < 0:  # 逆时针
        angle = -angle
        for point in points:
            x, y = point
            newx = round((x-cx)*math.cos(angle) - (y-cy)*math.sin(angle) + cx,1)
            newy = round((x-cx)*math.sin(angle) + (y-cy)*math.cos(angle) + cy,1)
            newpoints.append([newx, newy])
    else:
        for point in points:
            x, y = point
            newx = round((x-cx)*math.cos(angle) + (y-cy)*math.sin(angle) + cx,1)
            newy = round((y-cy)*math.cos(angle) - (x-cx)*math.sin(angle) + cy,1)
            newpoints.append([newx, newy])
    return newpoints
```
从xml到目标txt，完整代码如下：

首先把xml放在一个单独的文件夹里，然后执行程序会在xml同级目录生成txt文件夹
```py
import os
import math
import xml.etree.ElementTree as ET
import numpy as np
import cv2

def rotate(cx, cy, w, h, angle):
    # 用于常规坐标时，angle是顺时针旋转角度
    # 用于图像坐标时，angle是逆时针旋转角度
    # math.cos(angle)这里的angle单位是rad
    # angle = angle/180*math.pi  单位转换
    angle=-angle#这里是图像坐标
    points = [[cx-w/2, cy-h/2], [cx+w/2, cy-h/2],
              [cx+w/2, cy+h/2], [cx-w/2, cy+h/2]]
    newpoints = []
    if angle < 0:  # 逆时针
        angle = -angle
        for point in points:
            x, y = point
            newx = round((x-cx)*math.cos(angle) - (y-cy)*math.sin(angle) + cx,1)
            newy = round((x-cx)*math.sin(angle) + (y-cy)*math.cos(angle) + cy,1)
            newpoints.append([newx, newy])
    else:
        for point in points:
            x, y = point
            newx = round((x-cx)*math.cos(angle) + (y-cy)*math.sin(angle) + cx,1)
            newy = round((y-cy)*math.cos(angle) - (x-cx)*math.sin(angle) + cy,1)
            newpoints.append([newx, newy])
    return newpoints

def roxml2txt(dir):
    #dir是xml文件目录
    files = os.listdir(dir)
    parentdir,dirname = os.path.split(dir)
    txtdir=os.path.join(parentdir,'txt')
    if not os.path.exists(txtdir):
        os.mkdir(txtdir)
    for f in files:
        xml = ET.parse(os.path.join(dir,f))
        root = xml.getroot()
        boxes = root.iter('robndbox')
        with open(os.path.join(txtdir,f.split('.')[0]+'.txt'),'w+') as t:
            for box in boxes:
                cx = float(box.find('cx').text)
                cy = float(box.find('cy').text)
                w = float(box.find('w').text)
                h = float(box.find('h').text)
                angle = float(box.find('angle').text)
                newpoints = rotate(cx, cy, w, h, angle)#计算旋转后的4个点坐标
                # 用于查看坐标转换是否正确，在原图上画矩形框，自行修改图片路径
                newpoints=np.array(newpoints)
                newpoints= newpoints.astype(int)
                img=cv2.imread(os.path.join('test','images',f.split('.')[0]+'.png'))
                img=cv2.polylines(img,[newpoints],isClosed=True,color=(0,0,255))
                cv2.imshow('pic',img)
                cv2.waitKey()
                ##########################################
                line=''
                for point in newpoints:
                    line+=str(point[0])+' '+str(point[1])+' '
                line+='word 0\n'
                t.write(line)
                print(line)
            t.close()

roxml2txt('test/xml')#路径最后不能加/
```
为了检验坐标转换是否正确，加入了一段画图代码
```py
newpoints=np.array(newpoints)
newpoints= newpoints.astype(int)
img=cv2.imread(os.path.join('test','images',f.split('.')[0]+'.png'))
img=cv2.polylines(img,[newpoints],isClosed=True,color=(0,0,255))
cv2.imshow('pic',img)
cv2.waitKey()
```
效果如下，说明是正确的

![此图片的alt属性为空；文件名为image-4.png](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/04/image-4.png)

## 数据集放进项目目录

然后把图片文件夹改名为images，把txt文件夹改名为labelTxt（不改也行，这样显得规范）

在dataset目录下新建一个文件夹，存放images和labelTxt

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/04/image-10.png)

## 训练

首先要下载预训练模型，应该在github上的yolov5项目里，下载yolov5s.pt即可

[ultralytics/yolov5: YOLOv5 🚀 in PyTorch > ONNX > CoreML > TFLite (github.com)](https://github.com/ultralytics/yolov5)

如果是linux，直接使用作者提供的脚本

    pwd=yolov5_obb/ 执行

    bash data/scripts/download_weights.sh

会自动下载权重文件到yolov5_obb/，为了好看一点还是新建一个weights文件夹来存放

### 修改yolov5_obb/DOTA_devkit/dota_utils.py

classnames改为自己的类别

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/04/image-11.png)

### 修改yolov5_obb/data/dotav15_poly.yaml

修改数据集的目录

train,val,test都是与path间的相对路径，我这里图方便设为同一个了

然后修改nc类别数和names类别名即可

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/04/image-12.png)

### 修改yolov5_obb/train.py

修改参数

    weights:/weights/yolov5s.pt，下载的预训练模型路径  
    data:/data/dotav15_poly.yaml，刚刚修改的配置文件路径  
    epochs:100，训练次数  
    batch-size:8，一批的数量  
    imgsz:300，图片大小  
    device:0，显卡索引，没有的话写cpu  
    workers:8,线程数，出错的话就不断减小

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/04/image-13-1024x296.png)

执行python train.py即可训练

结果保存在runs/train/exp.../

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/04/image-14.png)

## 预测

修改yolov5_obb/detect.py

weights路径改为上面训练完生成的权重文件

source是用于预测的图片路径

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/04/image-15-1024x310.png)

预测结果保存在runs/detect/exp../下

![](https://cdn.staticaly.com/gh/xinhaojin/imgs-host@master/past/2022/04/image-17.png)

这是35张图片300次训练的结果，可以扩大数据集并且把训练集验证集分离，应该会有好的效果