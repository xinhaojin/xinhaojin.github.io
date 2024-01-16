---
title: labelme标注图片并制作voc数据集
index_img: https://xinhaojin.github.io/imgs-host/past/2022/02/image-12.png
tags: []
id: '2146'
categories:
  - - 目标检测
date: 2022-02-26 19:07:27
---

### 安装labelme

[wkentaro/labelme: Image Polygonal Annotation with Python (polygon, rectangle, circle, line, point and image-level flag annotation). (github.com)](https://github.com/wkentaro/labelme)

    pip install labelme

如果安装过，建议升级成新版本

    pip install -U labelme

### 使用labelme

打开命令行，输入labelme回车即可，不要关闭命令行

在图形界面中选择打开图片所在文件夹

![](https://xinhaojin.github.io/imgs-host/past/2022/02/image-11.png)

然后在file中修改3条设置

勾选自动保存（图标有蓝色阴影就是选上了）

修改输出文件夹，一般不要和图片放在同一个文件夹里，选择另一个文件夹

**纠正：不建议修改**

取消勾选保存图像数据，否则json很大，没有必要

**纠正：最好勾选保存图像数据**，否则如果想转换其他数据集格式如coco好像不行

![](https://xinhaojin.github.io/imgs-host/past/2022/02/image-13.png)

然后ctrl+r快捷键标矩形框就行，点一下放开，再点一下，然后输入标签点击OK即可

![](https://xinhaojin.github.io/imgs-host/past/2022/02/image-12.png)

### 转换为voc数据集

下载labelme2voc.py

![](https://xinhaojin.github.io/imgs-host/past/2022/02/image-14.png)

新建labels.txt存放标签

__ignore__和_background_是必须的，其他标签为自定义，比如我只需要检测验证码图片中文字的位置，那么只有一个标签就是word

![](https://xinhaojin.github.io/imgs-host/past/2022/02/image-16.png)

此时有一个文件夹,比如images，里面包含了所有图片和所有标注后的json文件

把labelme2voc.py和labels.txt放到和images同级目录下，执行

python labelme2voc.py images voc --labels labels.txt

images是输入文件夹名，即标注后保存的json所在文件夹

voc是输出文件夹名，不能是已存在的文件夹

![](https://xinhaojin.github.io/imgs-host/past/2022/02/image-15.png)

然后就在当前目录下生成了一个voc文件夹，4个内容，分别是

xml格式的标注文件夹

带标注的图片文件夹

原图片文件夹

存有标签名的文本

![](https://xinhaojin.github.io/imgs-host/past/2022/02/image-19.png)