---
title: VOC格式数据集转YOLO格式
tags: []

categories:
  - 目标检测
date: 2023-04-14 15:31:30
---

## VOC格式
```text
VOC
--Annotations
    00001.xml
        <annotation>
            <size>
                <width>320</width>
                <height>160</height>
                <depth>3</depth>
            </size>
            <segmented>0</segmented>
            <object>
                <name>word</name>
                <pose>Unspecified</pose>
                <truncated>0</truncated>
                <difficult>0</difficult>
                <bndbox>
                    <xmin>234</xmin>
                    <ymin>27</ymin>
                    <xmax>267</xmax>
                    <ymax>63</ymax>
                </bndbox>
            </object>
        </annotation>
    00002.xml
    00003.xml
    00004.xml
--JPEGImages
    00001.png
    00002.png
    00003.png
    00004.png
--ImageSets
    --Main
        train.txt
            00001
            00004
        val.txt
            00002
        test.txt
            00003
```
## YOLO格式
```text
YOLO
--images
    --train
        00001.jpg
        00004.jpg
    --val
        00002.jpg
    --test
        00003.jpg
--labels
    --train
        00001.txt
            0 0.7828125 0.28125 0.10312500000000001 0.225
            0 0.2328125 0.603125 0.109375 0.20625000000000002
            0 0.5703125 0.596875 0.115625 0.21875
            0 0.74375 0.7406250000000001 0.09375 0.19375
        00004.txt
    --val
        00002.txt
    --test
        00003.txt
```
## 代码
```python
import os
import random
import shutil
import xml.etree.ElementTree as ET


class_names = ['','']
voc_dir = '.'
yolo_dir = '.'

# 创建yolo格式的目录结构
os.makedirs(os.path.join(yolo_dir, 'images', 'train'), exist_ok=True)
os.makedirs(os.path.join(yolo_dir, 'images', 'val'), exist_ok=True)
os.makedirs(os.path.join(yolo_dir, 'images', 'test'), exist_ok=True)
os.makedirs(os.path.join(yolo_dir, 'labels', 'train'), exist_ok=True)
os.makedirs(os.path.join(yolo_dir, 'labels', 'val'), exist_ok=True)
os.makedirs(os.path.join(yolo_dir, 'labels', 'test'), exist_ok=True)

# 读取训练集、验证集和测试集的文件名
with open(os.path.join(voc_dir, 'ImageSets', 'Main', 'train.txt')) as f:
    train_list = f.read().splitlines()
with open(os.path.join(voc_dir, 'ImageSets', 'Main', 'val.txt')) as f:
    val_list = f.read().splitlines()
with open(os.path.join(voc_dir, 'ImageSets', 'Main', 'test.txt')) as f:
    test_list = f.read().splitlines()

# 转换标注文件格式
for split_name, split_list in [('train', train_list), ('val', val_list), ('test', test_list)]:
    for image_name in split_list:
        # 复制图片
        src_image_path = os.path.join(voc_dir, 'JPEGImages', f'{image_name}.png')
        dst_image_path = os.path.join(yolo_dir, 'images', split_name, f'{image_name}.jpg')
        shutil.copy(src_image_path, dst_image_path)
        
        # 解析XML标注文件
        xml_path = os.path.join(voc_dir, 'Annotations', f'{image_name}.xml')
        tree = ET.parse(xml_path)
        root = tree.getroot()

        # 获取图片大小
        size_elem = root.find('size')
        width = int(size_elem.find('width').text)
        height = int(size_elem.find('height').text)

        # 转换标注
        with open(os.path.join(yolo_dir, 'labels', split_name, f'{image_name}.txt'), 'w') as f:
            for obj_elem in root.findall('object'):
                class_name = obj_elem.find('name').text
                if class_name not in class_names:
                    continue

                bbox_elem = obj_elem.find('bndbox')
                xmin = int(float(bbox_elem.find('xmin').text))
                ymin = int(float(bbox_elem.find('ymin').text))
                xmax = int(float(bbox_elem.find('xmax').text))
                ymax = int(float(bbox_elem.find('ymax').text))

                x_center = (xmin + xmax) / 2 / width
                y_center = (ymin + ymax) / 2 / height
                w = (xmax - xmin) / width
                h = (ymax - ymin) / height

                # 写入标注
                f.write(f"{class_names.index(class_name)} {x_center:.6f} {y_center:.6f} {w:.6f} {h:.6f}\n")
```