---
title: yolov5单目标检测简化改进
tags: []
categories:
  - 目标检测
date: 2022-12-07 14:22:00
---
backbone部分,去掉了7.8两层的conv和c3,只做到16倍下采样

head部分,去掉所有concat,3个检测层改为1个

Model size:3.63M->1.01M

Params:1.7M->0.45M

FLOPs:4.1G->2.8G

mAP:81.46->81.96

主要原因应该是对于我的检测任务，待检测目标的大小几乎不会有很大的变化，且和16倍下采样后每个cell的感受野大小一致。
### 原yolov5n.yaml
```yaml
# YOLOv5 🚀 by Ultralytics, GPL-3.0 license

# Parameters
nc: 1  # number of classes
depth_multiple: 0.33  # model depth multiple
width_multiple: 0.25  # layer channel multiple
anchors:
  - [10,13, 16,30, 33,23]  # P3/8
  - [30,61, 62,45, 59,119]  # P4/16
  - [116,90, 156,198, 373,326]  # P5/32

# YOLOv5 v6.0 backbone
backbone:
  # [from, number, module, args]
  [[-1, 1, Conv, [64, 6, 2, 2]],  # 0-P1/2
   [-1, 1, Conv, [128, 3, 2]],  # 1-P2/4
   [-1, 3, C3, [128]],
   [-1, 1, Conv, [256, 3, 2]],  # 3-P3/8
   [-1, 6, C3, [256]],
   [-1, 1, Conv, [512, 3, 2]],  # 5-P4/16
   [-1, 9, C3, [512]],
   [-1, 1, Conv, [1024, 3, 2]],  # 7-P5/32
   [-1, 3, C3, [1024]],
   [-1, 1, SPPF, [1024, 5]],  # 9
  ]

# YOLOv5 v6.0 head
head:
  [[-1, 1, Conv, [512, 1, 1]],
   [-1, 1, nn.Upsample, [None, 2, 'nearest']],
   [[-1, 6], 1, Concat, [1]],  # cat backbone P4
   [-1, 3, C3, [512, False]],  # 13

   [-1, 1, Conv, [256, 1, 1]],
   [-1, 1, nn.Upsample, [None, 2, 'nearest']],
   [[-1, 4], 1, Concat, [1]],  # cat backbone P3
   [-1, 3, C3, [256, False]],  # 17 (P3/8-small)

   [-1, 1, Conv, [256, 3, 2]],
   [[-1, 14], 1, Concat, [1]],  # cat head P4
   [-1, 3, C3, [512, False]],  # 20 (P4/16-medium)

   [-1, 1, Conv, [512, 3, 2]],
   [[-1, 10], 1, Concat, [1]],  # cat head P5
   [-1, 3, C3, [1024, False]],  # 23 (P5/32-large)

   [[17, 20, 23], 1, Detect, [nc, anchors]],  # Detect(P3, P4, P5)
  ]
```
### yolov5_new.yaml
```yaml
# YOLOv5 🚀 by Ultralytics, GPL-3.0 license

# Parameters
nc: 1  # number of classes
depth_multiple: 1  # model depth multiple
width_multiple: 1  # layer channel multiple
anchors:
  - [71.741,66.536,69.588,70.286,73.263,72.851]

# YOLOv5 v6.0 backbone
backbone:
  # [from, number, module, args]
  [[-1, 1, Conv, [16, 6, 2, 2]],  # 0-P1/2
   [-1, 1, Conv, [32, 3, 2]],  # 1-P2/4
   [-1, 1, C3, [32]],
   [-1, 1, Conv, [64, 3, 2]],  # 3-P3/8 
   [-1, 2, C3, [64]],
   [-1, 1, Conv, [128, 3, 2]],  # 5-P4/16 
   [-1, 3, C3, [128]],
   [-1, 1, SPPF, [256, 5]],  # 7
  ]

# YOLOv5 v6.0 head
head:
  [
   [-1, 1, Conv, [128, 1, 1]],
   [-1, 1, C3, [128, False]],  # 9 (P4/16-medium)

   [[9], 1, Detect, [nc, anchors]],  # Detect(P4)
  ]
```